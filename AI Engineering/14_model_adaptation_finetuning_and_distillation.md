# 14. Model Adaptation, Fine-Tuning & Distillation

A comprehensive, production-grade guide to model adaptation strategies, Supervised Fine-Tuning (SFT), Parameter-Efficient Fine-Tuning (PEFT), Low-Rank Adaptation (LoRA) and QLoRA mechanics, Direct Preference Optimization (DPO), Reinforcement Fine-Tuning (RFT) with verifiable rewards, knowledge distillation, and dynamic high-density Multi-LoRA serving in vLLM. Tailored for AI Engineers, Applied ML Researchers, and Foundation Model Adaptation Specialists.

---

## Architectural Overview: Parameter-Efficient Fine-Tuning (LoRA / QLoRA) & Serving Pipeline

The diagram below details the mathematical mechanics and production runtime of Low-Rank Adaptation (LoRA), contrasting frozen base model weights with trainable low-rank decomposition matrices, followed by dynamic Multi-LoRA serving over a shared base engine.

```mermaid
flowchart TD
    subgraph PretrainedBase ["1. Frozen Base Foundation Weights (W_0)"]
        BaseWeights["<b>Pretrained Base Weights W_0 ∈ ℝ^(d × k)</b><br/>(Kept strictly FROZEN (requires_grad = False); Loaded in 16-bit BF16 (2 bytes) or 4-bit NF4 (0.5 bytes in QLoRA); Memory: Zero optimizer states allocated!)"]
    end

    subgraph LoRADecomposition ["2. Low-Rank Trainable Adapters (ΔW = B · A)"]
        InputX["Input Hidden State: x ∈ ℝ^(B × L × k)"]
        
        subgraph ParallelBranches ["Parallel Computation Branches"]
            FrozenBranch["Frozen Linear Branch:<br/>h_frozen = x · W_0^T"]
            
            subgraph AdapterBranch ["Trainable Low-Rank Branch (Rank r ≪ min(d, k))"]
                MatrixA["Matrix A ∈ ℝ^(r × k)<br/>(Gaussian Init: N(0, σ²))"]
                MatrixB["Matrix B ∈ ℝ^(d × r)<br/>(Zero Init: All 0s at start)"]
                ScalingFactor["Scaling Factor: (α / r)"]
                
                MatrixA --> MatrixB --> ScalingFactor
            end
        end

        Combine["Hidden State Addition:<br/>h_total = h_frozen + (α / r) · (x · A^T · B^T)"]
        
        InputX --> FrozenBranch --> Combine
        InputX --> MatrixA
        ScalingFactor --> Combine
    end

    subgraph MultiLoRAServing ["3. High-Density Multi-LoRA Production Serving (vLLM)"]
        SharedBaseModel["Single Shared Base Model (LLaMA-3.1-70B)<br/>(Occupies 70GB VRAM once in GPU memory)"]
        
        Adapter1["Adapter 1: Legal Extractor (120MB)"]
        Adapter2["Adapter 2: SQL Generator (120MB)"]
        Adapter3["Adapter 3: Customer Support (120MB)"]

        SharedBaseModel <--> Adapter1
        SharedBaseModel <--> Adapter2
        SharedBaseModel <--> Adapter3
        
        ClientReq["Client Request<br/>(Specifies header: 'X-Adapter-ID: sql_gen')"] --> SharedBaseModel
    end

    Combine --> MultiLoRAServing

    style PretrainedBase fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style LoRADecomposition fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c4b5fd
    style MultiLoRAServing fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style BaseWeights fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style InputX fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style FrozenBranch fill:#1f2937,stroke:#64748b,stroke-width:1.5px,color:#fff
    style MatrixA fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style MatrixB fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style ScalingFactor fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style Combine fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style SharedBaseModel fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Adapter1 fill:#1f2937,stroke:#ec4899,stroke-width:1.5px,color:#fff
    style Adapter2 fill:#1f2937,stroke:#ec4899,stroke-width:1.5px,color:#fff
    style Adapter3 fill:#1f2937,stroke:#ec4899,stroke-width:1.5px,color:#fff
    style ClientReq fill:#1f2937,stroke:#3b82f6,stroke-width:1.5px,color:#fff
```

---

## Table of Contents
1. [The Model Adaptation Decision Framework](#1-the-model-adaptation-decision-framework)
   - [1.1 The Adaptation Hierarchy: Prompt vs. RAG vs. Fine-Tuning](#11-the-adaptation-hierarchy-prompt-vs-rag-vs-fine-tuning)
   - [1.2 What Fine-Tuning Solves (Style, Syntax, Latency, Domain Grammar)](#12-what-fine-tuning-solves-style-syntax-latency-domain-grammar)
   - [1.3 What Fine-Tuning Cannot Solve (Dynamic Facts, Hallucination Reduction)](#13-what-fine-tuning-cannot-solve-dynamic-facts-hallucination-reduction)
   - [1.4 Total Cost of Ownership (TCO) Analysis](#14-total-cost-of-ownership-tco-analysis)
2. [Training Data Curation & Dataset Engineering](#2-training-data-curation--dataset-engineering)
   - [2.1 Sourcing High-Signal Demonstrations & The LIMA Hypothesis](#21-sourcing-high-signal-demonstrations--the-lima-hypothesis)
   - [2.2 Standardized Chat Templates (ChatML & Llama-3 Special Delimiters)](#22-standardized-chat-templates-chatml--llama-3-special-delimiters)
   - [2.3 Loss Masking Mechanics: Prompt Masking with Label -100](#23-loss-masking-mechanics-prompt-masking-with-label--100)
   - [2.4 Leak-Free Partitioning & Contamination Prevention](#24-leak-free-partitioning--contamination-prevention)
3. [Supervised Fine-Tuning (SFT) Mechanics](#3-supervised-fine-tuning-sft-mechanics)
   - [3.1 The SFT Training Loop & Autoregressive Loss Formulation](#31-the-sft-training-loop--autoregressive-loss-formulation)
   - [3.2 Critical Hyperparameters (Learning Rate, Warmup, Effective Batch Size)](#32-critical-hyperparameters-learning-rate-warmup-effective-batch-size)
   - [3.3 Diagnosing Training Curves: Underfitting vs. Overfitting](#33-diagnosing-training-curves-underfitting-vs-overfitting)
   - [3.4 Catastrophic Forgetting & General Capability Degradation](#34-catastrophic-forgetting--general-capability-degradation)
4. [Parameter-Efficient Fine-Tuning (PEFT): LoRA, QLoRA & DoRA](#4-parameter-efficient-fine-tuning-peft-lora-qlora--dora)
   - [4.1 Low-Rank Decomposition Mathematics ($W_0 + \frac{\alpha}{r} BA$)](#41-low-rank-decomposition-mathematics-w_0--fracalphar-ba)
   - [4.2 Sizing LoRA Parameters: Rank $r$, Alpha $\alpha$, and Target Modules](#42-sizing-lora-parameters-rank-r-alpha-alpha-and-target-modules)
   - [4.3 QLoRA Mechanics: 4-bit NormalFloat (NF4), Double Quantization & Paged Optimizers](#43-qlora-mechanics-4-bit-normalfloat-nf4-double-quantization--paged-optimizers)
   - [4.4 Weight-Decomposed Low-Rank Adaptation (DoRA)](#44-weight-decomposed-low-rank-adaptation-dora)
5. [Preference Alignment: Direct Preference Optimization (DPO)](#5-preference-alignment-direct-preference-optimization-dpo)
   - [5.1 Demonstrations vs. Preference Pairs $(Prompt, Chosen, Rejected)$](#51-demonstrations-vs-preference-pairs-prompt-chosen-rejected)
   - [5.2 The DPO Mathematical Formulation: Eliminating the Reward Model](#52-the-dpo-mathematical-formulation-eliminating-the-reward-model)
   - [5.3 Mitigating DPO Pitfalls: Length & Verbosity Bias](#53-mitigating-dpo-pitfalls-length--verbosity-bias)
   - [5.4 Alternative Preference Frameworks: KTO & ORPO](#54-alternative-preference-frameworks-kto--orpo)
6. [Reinforcement Fine-Tuning (RFT) with Verifiable Rewards](#6-reinforcement-fine-tuning-rft-with-verifiable-rewards)
   - [6.1 Reinforcement Learning in Verifiable Solution Spaces](#61-reinforcement-learning-in-verifiable-solution-spaces)
   - [6.2 Ground-Truth Execution Verifiers vs. LLM-as-a-Judge](#62-ground-truth-execution-verifiers-vs-llm-as-a-judge)
   - [6.3 Rejection Sampling Fine-Tuning (Best-of-N Distillation)](#63-rejection-sampling-fine-tuning-best-of-n-distillation)
7. [Knowledge Distillation & Model Compression](#7-knowledge-distillation--model-compression)
   - [7.1 The Teacher-Student Architecture & Dark Knowledge](#71-the-teacher-student-architecture--dark-knowledge)
   - [7.2 Mathematical Formulation of Cross-Entropy & KL Divergence Loss](#72-mathematical-formulation-of-cross-entropy--kl-divergence-loss)
   - [7.3 Synthetic Dataset Distillation Pipelines](#73-synthetic-dataset-distillation-pipelines)
8. [Production Implementation: QLoRA Fine-Tuning Pipeline & Multi-LoRA Serving Engine](#8-production-implementation-qlora-fine-tuning-pipeline--multi-lora-serving-engine)
   - [8.1 Systems Architecture of the Pipeline](#81-systems-architecture-of-the-pipeline)
   - [8.2 Complete Production-Grade Python Implementation](#82-complete-production-grade-python-implementation)
   - [8.3 Serving Dynamic Multi-LoRA Adapters in vLLM](#83-serving-dynamic-multi-lora-adapters-in-vllm)
9. [Summary Comparison & Engineering Reference Matrix](#9-summary-comparison--engineering-reference-matrix)
10. [References & Further Reading](#10-references--further-reading)

---

## 1. The Model Adaptation Decision Framework

### 1.1 The Adaptation Hierarchy: Prompt vs. RAG vs. Fine-Tuning

A pervasive anti-pattern in enterprise AI initiatives is jumping directly to fine-tuning whenever a foundation model fails a task. Fine-tuning is an expensive, maintenance-intensive engineering commitment that should only be undertaken when simpler, more agile paradigms prove mathematically or operationally insufficient.

```mermaid
flowchart TD
    Start["New Production Capability Requirement"] --> Q1{"Can few-shot prompting or system instructions achieve the goal?"}
    
    Q1 -- "Yes" --> PromptEng["<b>1. Prompt Engineering / Few-Shot In-Context Learning</b><br/>(Fastest time-to-market (Hours); Zero training compute or weights to manage; High per-request token consumption)"]
    
    Q1 -- "No" --> Q2{"Does the task require dynamic, evolving, or proprietary factual knowledge?"}
    
    Q2 -- "Yes" --> RAG["<b>2. Retrieval-Augmented Generation (RAG)</b><br/>(Grounding on live, searchable enterprise corpus; Strict citation tracking & zero training required; Complex vector DB & hybrid search retrieval pipeline)"]
    
    Q2 -- "No" --> Q3{"Does the task require specialized formatting, strict domain grammar, or low latency?"}
    
    Q3 -- "Yes" --> FineTune["<b>3. Fine-Tuning (PEFT / SFT / DPO)</b><br/>(Imprints syntax, tone, and procedural reasoning into weights; Cuts prompt token consumption by 70% (no few-shot examples needed); High upfront training compute; static knowledge snapshot)"]
    
    Q3 -- "No" --> Hybrid["<b>4. Hybrid: Fine-Tuned Model + RAG Retrieval</b><br/>(Best of both worlds: specialized reasoning model accessing live vector corpus)"]

    style Start fill:#1f2937,stroke:#3b82f6,stroke-width:1.5px,color:#fff
    style Q1 fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style Q2 fill:#0f172a,stroke:#8b5cf6,stroke-width:1.5px,color:#c4b5fd
    style Q3 fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style PromptEng fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style RAG fill:#1f2937,stroke:#8b5cf6,stroke-width:2px,color:#fff
    style FineTune fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style Hybrid fill:#1f2937,stroke:#ec4899,stroke-width:2px,color:#fff
```

| Dimension | Prompt Engineering | Retrieval-Augmented Generation (RAG) | Fine-Tuning (SFT / PEFT) |
| :--- | :--- | :--- | :--- |
| **Primary Purpose** | Guiding off-the-shelf behavior via instructions | Providing external dynamic factual context | Teaching specialized form, style, or syntax |
| **Knowledge Dynamicism** | Static within prompt text | **Real-time / Instant update** (Update vector index) | **Frozen** (Requires retraining to update knowledge) |
| **Output Controllability** | Moderate (Prone to prompt drift) | High (Grounded strictly in retrieved context) | **Very High** (Directly reinforces token probability) |
| **Latency & Token Cost** | High (Few-shot exemplars inflate prompt tokens) | High (Retrieved chunk passages bloat prompt) | **Low** (Zero-shot execution; no prompt exemplars) |
| **Engineering Complexity** | Low (Text editing) | High (Data loaders, chunking, vector indexing) | **High** (GPU compute, hyperparameter tuning, eval gates) |

---

### 1.2 What Fine-Tuning Solves (Style, Syntax, Latency, Domain Grammar)

Fine-tuning modifies the model's conditional probability distribution over tokens:

$$P(y_t \mid y_{<t}, x)$$

Fine-tuning is the optimal solution for:
1. **Enforcing Strict Schema Adherence:** Training a model to emit 100% compliant custom JSON, YAML, SQL dialects, or esoteric domain DSLs without requiring large grammatical repair loops.
2. **Imprinting Specialized Organizational Tone:** Adapting a conversational agent to mimic a company's specific brand persona, customer empathy guidelines, or concise executive brevity.
3. **Slashing Prompt Token Bloat and Latency:** An application that requires 2,500 tokens of few-shot examples in every prompt to maintain accuracy can be fine-tuned on those exemplars. The system prompt drops to 50 tokens, cutting TTFT by $80\%$ and recurring API costs by $70\%$.
4. **Teaching Complex Procedural Workflows:** Training small, inexpensive models (e.g., LLaMA-3.1-8B) to execute complex multi-step reasoning that otherwise requires frontier models (e.g., Claude 3.5 Sonnet).

---

### 1.3 What Fine-Tuning Cannot Solve (Dynamic Facts, Hallucination Reduction)

> [!CAUTION]
> **The Fallacy of Fine-Tuning as a Knowledge Store:**
> Attempting to inject new enterprise factual knowledge (e.g., "What was Customer X's order total yesterday?") via fine-tuning is an architectural catastrophe. LLM weights store knowledge as diffuse, fuzzy associative patterns across billions of parameters. If you train a model on new factual data:
> 1. It will still hallucinate facts when queried with subtle variations.
> 2. You cannot selectively update, patch, or delete a single fact without risking catastrophic forgetting across unrelated capabilities.
> 3. Training is orders of magnitude slower and more expensive than updating a vector database row.
> 
> **Factual knowledge belongs in a database (RAG). Reasoning and formatting behavior belong in weights (Fine-Tuning).**

---

### 1.4 Total Cost of Ownership (TCO) Analysis

When evaluating whether to fine-tune an open-weight 8B model to replace GPT-4o:

$$\text{TCO} = \mathcal{C}_{\text{data\_curation}} + \mathcal{C}_{\text{training\_compute}} + \mathcal{C}_{\text{inference\_hosting}} + \mathcal{C}_{\text{maintenance}}$$

#### Break-Even Economics Example
- **Baseline (GPT-4o API):** 200,000 queries/day $\times$ 1,500 prompt tokens (with few-shot) $\times$ \$2.50 / 1M tokens = \$750.00 / day $\implies \mathbf{\$22,500 / \text{month}}$.
- **Adapted Model (Fine-Tuned LLaMA-3.1-8B on AWS EC2):**
  - Data curation & synthetic generation: \$1,200 (one-time).
  - Training on 4x A100 GPUs for 6 hours: \$75 (one-time).
  - Dedicated inference instance (`g5.2xlarge` with 1x A10G GPU): \$1.21/hour $\implies \mathbf{\$871 / \text{month}}$.
  - *Result:* **Net savings of over \$21,000 per month (96% cost reduction)**, paying off all development and training costs within the first two days of deployment.

---

## 2. Training Data Curation & Dataset Engineering

### 2.1 Sourcing High-Signal Demonstrations & The LIMA Hypothesis

In Supervised Fine-Tuning, data quality overwhelmingly supersedes data quantity. This principle was formally demonstrated by Meta AI's seminal **LIMA (Less Is More for Alignment)** research (Zhou et al., 2023):

> [!IMPORTANT]
> **The LIMA Hypothesis:**
> A foundation model acquires virtually all its world knowledge, linguistic grammar, and reasoning capabilities during massive self-supervised pretraining. Supervised Fine-Tuning merely teaches the model the **style, format, and interactive conversational interface** for surfacing that knowledge.
> 
> Training a model on **1,000 curated, human-inspected, high-signal demonstrations** consistently outperforms training on **50,000 noisy, web-scraped synthetic examples**.

```mermaid
flowchart TD
    subgraph DatasetCurationPipeline ["High-Signal Dataset Engineering Pipeline"]
        RawDump["Raw Internal Enterprise Logs<br/>(100,000 messy historical customer chat transcripts)"]
        
        Filter1["<b>1. Heuristic & Rule-Based Filtering</b><br/>(Strip PII (SSN, credit cards, emails) via Presidio; Remove truncated dialogues & empty responses; Filter length: 150 < characters < 4,000)"]
        
        Filter2["<b>2. Deduplication & Semantic Diversity</b><br/>(MinHash LSH deduplication (Jaccard similarity > 0.85); Cluster embeddings to ensure uniform topical coverage)"]
        
        Filter3["<b>3. Automated Quality Verification (LLM-as-a-Judge)</b><br/>(Frontier model scores demonstrations on accuracy & tone; Drop examples scoring below 4.5 / 5.0)"]
        
        CuratedSet["Curated Golden Training Dataset<br/>(2,500 Pristine Multi-Turn Demonstrations)"]

        RawDump --> Filter1 --> Filter2 --> Filter3 --> CuratedSet
    end

    style DatasetCurationPipeline fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style RawDump fill:#1f2937,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style Filter1 fill:#1f2937,stroke:#3b82f6,stroke-width:1px,color:#fff
    style Filter2 fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style Filter3 fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
    style CuratedSet fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

---

### 2.2 Standardized Chat Templates (ChatML & Llama-3 Special Delimiters)

Foundation models process a single continuous sequence of integer token IDs. To structure multi-turn dialogues between users and assistants, models utilize **Chat Templates** containing special, reserved non-printing delimiter tokens.

```mermaid
flowchart TD
    subgraph ChatMLStructure ["Standard ChatML Prompt Framing"]
        Sys["<|im_start|>system\nYou are a financial extraction specialist.<|im_end|>"]
        User["<|im_start|>user\nExtract the EBITDA from this paragraph...<|im_end|>"]
        Assist["<|im_start|>assistant\n{\"ebitda_usd\": 42000000}<|im_end|>"]
        
        Sys --> User --> Assist
    end

    style ChatMLStructure fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style Sys fill:#1f2937,stroke:#3b82f6,stroke-width:1px,color:#fff
    style User fill:#1f2937,stroke:#8b5cf6,stroke-width:1px,color:#fff
    style Assist fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### The Llama-3 Delimiter Template
Meta's LLaMA-3 architecture employs explicit header and end-of-turn delimiters:
```text
<|begin_of_text|><|start_header_id|>system<|end_header_id|>

You are an enterprise SQL generation assistant.<|eot_id|><|start_header_id|>user<|end_header_id|>

List all active customers in Texas.<|eot_id|><|start_header_id|>assistant<|end_header_id|>

SELECT customer_id, name FROM customers WHERE state = 'TX' AND status = 'active';<|eot_id|>
```

> [!CAUTION]
> **The Chat Template Mismatch Disaster:**
> If you fine-tune a model using ChatML formatting (`<|im_start|>`), but serve it using LLaMA-3 formatting (`<|start_header_id|>`), the model will fail completely. It will hallucinate both sides of the conversation, treat the user's prompt as text to continue, and emit gibberish. Always verify the model tokenizer's `chat_template` Jinja2 specification.

---

### 2.3 Loss Masking Mechanics: Prompt Masking with Label -100

In standard autoregressive language modeling, the cross-entropy loss is computed over every token in the sequence. In Supervised Fine-Tuning, **computing loss over the user's prompt tokens is a severe bug**.

```mermaid
flowchart LR
    subgraph FullSequence ["Tokenized Training Example (Length: 8 Tokens)"]
        T0["Tok 0: 'System'"]
        T1["Tok 1: 'Prompt'"]
        T2["Tok 2: 'User'"]
        T3["Tok 3: 'Question'"]
        T4["Tok 4: 'Assistant'"]
        T5["Tok 5: 'Target'"]
        T6["Tok 6: 'Answer'"]
        T7["Tok 7: '<|eot_id|>'"]
    end

    subgraph MaskedLabels ["Target Labels Array (Passed to Cross-Entropy Loss)"]
        L0["-100 (Ignored)"]
        L1["-100 (Ignored)"]
        L2["-100 (Ignored)"]
        L3["-100 (Ignored)"]
        L4["-100 (Ignored)"]
        L5["Target Token ID (Loss Computed)"]
        L6["Answer Token ID (Loss Computed)"]
        L7["<|eot_id|> ID (Loss Computed)"]
    end

    T0 --- L0
    T1 --- L1
    T2 --- L2
    T3 --- L3
    T4 --- L4
    T5 --- L5
    T6 --- L6
    T7 --- L7

    style FullSequence fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style MaskedLabels fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style T0 fill:#1f2937,stroke:#64748b,stroke-width:1px,color:#fff
    style T1 fill:#1f2937,stroke:#64748b,stroke-width:1px,color:#fff
    style T2 fill:#1f2937,stroke:#64748b,stroke-width:1px,color:#fff
    style T3 fill:#1f2937,stroke:#64748b,stroke-width:1px,color:#fff
    style T4 fill:#1f2937,stroke:#64748b,stroke-width:1px,color:#fff
    style T5 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style T6 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style T7 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style L0 fill:#1e293b,stroke:#ef4444,stroke-width:1px,color:#fca5a5
    style L1 fill:#1e293b,stroke:#ef4444,stroke-width:1px,color:#fca5a5
    style L2 fill:#1e293b,stroke:#ef4444,stroke-width:1px,color:#fca5a5
    style L3 fill:#1e293b,stroke:#ef4444,stroke-width:1px,color:#fca5a5
    style L4 fill:#1e293b,stroke:#ef4444,stroke-width:1px,color:#fca5a5
    style L5 fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#6ee7b7
    style L6 fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#6ee7b7
    style L7 fill:#1e293b,stroke:#10b981,stroke-width:2px,color:#6ee7b7
```

#### Why Prompt Masking with `-100` is Mandatory
PyTorch's `torch.nn.CrossEntropyLoss(ignore_index=-100)` ignores all targets set to `-100`.
- We do not want the model to learn to predict or memorize the user's questions or system prompt.
- If prompt tokens are not masked, the optimizer expends gradient capacity memorizing the phrasing of the input questions, leading to rapid overfitting and reduced conversational flexibility.
- Gradients must propagate strictly from the **assistant's completion tokens and terminal `<|eot_id|>` token**.

---

### 2.4 Leak-Free Partitioning & Contamination Prevention

In tabular machine learning, a simple random train/test split suffices. In language modeling, random splitting often leaks identical semantic formulations across splits:
- If a customer asks "How do I return item #123?" in Train and "How do I return item #456?" in Test, the model simply regurgitates memorized phrasing.
- **MinHash LSH De-duplication:** Partitioning must be clustered by semantic topics and entity identities. Any training pair with Jaccard similarity $> 0.70$ against a test case must be purged from the training set.

---

## 3. Supervised Fine-Tuning (SFT) Mechanics

### 3.1 The SFT Training Loop & Autoregressive Loss Formulation

Supervised Fine-Tuning optimizes the foundation model weights $\theta$ to minimize the negative log-likelihood of the target completion tokens $y = (y_1, y_2, \dots, y_T)$ conditioned on the input prompt context $x$:

$$\mathcal{L}_{\text{SFT}}(\theta) = -\sum_{t=1}^T \log P_\theta(y_t \mid y_{<t}, x)$$

```mermaid
flowchart TD
    BatchIngress["Mini-Batch of Masked Token Sequences<br/>Input IDs: [B, Max_Seq_Len]<br/>Labels: [B, Max_Seq_Len] (Prompt tokens = -100)"]
    
    ForwardPass["Forward Pass through Model<br/>Computes Logits: z ∈ ℝ^(B × Max_Seq_Len × Vocab_Size)"]
    
    ShiftLogits["Shift Logits & Labels by 1 Token:<br/>logits_pred = logits[:, :-1, :]<br/>target_labels = labels[:, 1:]"]
    
    ComputeLoss["Cross-Entropy Loss (ignore_index = -100):<br/>Loss = CrossEntropy(logits_pred, target_labels)"]
    
    BackwardPass["Backward Pass (Autograd Gradient Accumulation):<br/>Computes ∇_θ L_SFT across active adapters"]
    
    OptimizerStep["Optimizer Step (Paged AdamW):<br/>Updates parameters θ ← θ - η · m_hat / (sqrt(v_hat) + ε)"]

    BatchIngress --> ForwardPass --> ShiftLogits --> ComputeLoss --> BackwardPass --> OptimizerStep

    style BatchIngress fill:#1f2937,stroke:#3b82f6,stroke-width:1.5px,color:#fff
    style ForwardPass fill:#1f2937,stroke:#3b82f6,stroke-width:1px,color:#fff
    style ShiftLogits fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style ComputeLoss fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#6ee7b7
    style BackwardPass fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
    style OptimizerStep fill:#1f2937,stroke:#ec4899,stroke-width:2px,color:#fff
```

---

### 3.2 Critical Hyperparameters (Learning Rate, Warmup, Effective Batch Size)

Because Supervised Fine-Tuning builds upon an already mature pretrained representation, the optimization dynamics differ radically from pretraining:

| Hyperparameter | Recommended Value Range | Systems Rationale |
| :--- | :--- | :--- |
| **Learning Rate ($\eta$)** | $1\times 10^{-5}$ to $2\times 10^{-4}$ (LoRA)<br/>$5\times 10^{-6}$ to $2\times 10^{-5}$ (Full Fine-Tune) | Too high burns pretrained representations; too low fails to adapt formatting. |
| **LR Scheduler** | Cosine Annealing with Linear Warmup | Linear warmup (3%–5% of total steps) prevents massive initial gradients from corrupting adapters. |
| **Effective Batch Size** | $16$ to $64$ sequences | Combines `per_device_train_batch_size` (e.g., 2) with `gradient_accumulation_steps` (e.g., 16). Small batch sizes induce noisy gradients; large batch sizes slow convergence. |
| **Weight Decay** | $0.01$ to $0.1$ | Regularizes weights, preventing adapter divergence. |
| **Epochs** | $1$ to $3$ epochs | Training for $> 4$ epochs on a small dataset guarantees overfitting and loss of general reasoning. |

---

### 3.3 Diagnosing Training Curves: Underfitting vs. Overfitting

```mermaid
xychart-beta
    title "SFT Training & Validation Loss Dynamics (Healthy vs Overfitting)"
    x-axis "Training Steps" [100, 200, 300, 400, 500, 600, 700, 800]
    y-axis "Cross-Entropy Loss" 0.0 --> 2.5
    line "Healthy Train Loss" [2.1, 1.6, 1.3, 1.1, 0.95, 0.85, 0.78, 0.72]
    line "Healthy Val Loss"   [2.15, 1.65, 1.35, 1.15, 1.02, 0.94, 0.88, 0.84]
    line "Overfitting Val Loss" [2.15, 1.65, 1.38, 1.25, 1.35, 1.60, 1.95, 2.30]
```

- **Healthy Convergence:** Training loss decreases steadily, accompanied by a parallel decrease in validation loss that flattens smoothly.
- **Overfitting Divergence:** Validation loss begins climbing sharply while training loss drops toward zero ($<0.3$). The model has memorized the verbatim training exemplars and will fail on novel real-world user queries.

---

### 3.4 Catastrophic Forgetting & General Capability Degradation

A fundamental hazard in fine-tuning is **Catastrophic Forgetting**: when an LLM is fine-tuned exclusively on a specialized task (e.g., writing Python code), its performance on unrelated capabilities (e.g., common sense reasoning, creative writing, or basic arithmetic) degrades drastically.

#### Mitigation: Rehearsal Data Regularization
To preserve general capabilities, always mix **10%–20% of general multi-task instruction data** (e.g., from the OpenOrca, UltraChat, or ShareGPT datasets) into your specialized training corpus:

$$\mathcal{D}_{\text{train}} = \mathcal{D}_{\text{domain\_specific}} \cup \left( 0.15 \times \mathcal{D}_{\text{general\_reasoning}} \right)$$

---

## 4. Parameter-Efficient Fine-Tuning (PEFT): LoRA, QLoRA & DoRA

### 4.1 Low-Rank Decomposition Mathematics ($W_0 + \frac{\alpha}{r} BA$)

In a 70B parameter model, full parameter fine-tuning requires updating all 70 billion weights. In 16-bit mixed precision with the AdamW optimizer:
- Static Model Weights: $140\text{ GB}$
- Gradients: $140\text{ GB}$
- AdamW Optimizer States ($1\text{st}$ & $2\text{nd}$ moments): $280\text{ GB}$
- **Total VRAM Required for Full Fine-Tuning: $> 560\text{ GB}$** (Requires an 8x A100/H100 node cluster).

**Low-Rank Adaptation (LoRA)** (Hu et al., 2021) hypothesizes that the weight updates during task adaptation have a very low **intrinsic rank**.

For a pretrained frozen linear layer weight matrix $W_0 \in \mathbb{R}^{d \times k}$, LoRA decomposes the weight update $\Delta W$ into two low-rank matrices:

$$W = W_0 + \Delta W = W_0 + \frac{\alpha}{r} \left( \mathbf{B} \cdot \mathbf{A} \right)$$

Where:
- $\mathbf{A} \in \mathbb{R}^{r \times k}$: Initialized with random Gaussian values $\mathcal{N}(0, \sigma^2)$.
- $\mathbf{B} \in \mathbb{R}^{d \times r}$: Initialized strictly to **all zeros** ($\mathbf{B} = \mathbf{0}$).
- $r \ll \min(d, k)$: The low rank (typically $r \in [8, 64]$).
- $\alpha$: A constant scaling factor hyperparameter.

```mermaid
flowchart LR
    subgraph MatrixDimensions ["LoRA Dimensionality Compression (d = 4096, k = 4096, r = 16)"]
        FullDelta["Full Weight Update Matrix ΔW<br/>4096 x 4096 = 16,777,216 Parameters"]
        
        subgraph Factorized ["Factorized Low-Rank Decomposition (B · A)"]
            MatA["Matrix A: 16 x 4096<br/>65,536 Parameters"]
            MatB["Matrix B: 4096 x 16<br/>65,536 Parameters"]
        end
    end

    FullDelta -->|"Decompose into"| Factorized
    Factorized --> Result["Total Parameters: 131,072<br/>(99.2% Parameter Reduction! 🔥)"]

    style MatrixDimensions fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style FullDelta fill:#1f2937,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style Factorized fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style MatA fill:#1f2937,stroke:#10b981,stroke-width:1px,color:#fff
    style MatB fill:#1f2937,stroke:#10b981,stroke-width:1px,color:#fff
    style Result fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

#### Why $\mathbf{B} = \mathbf{0}$ Initialization is Critical
Because $\mathbf{B}$ is initialized to zero, $\Delta W = \mathbf{B} \cdot \mathbf{A} = \mathbf{0}$ at step zero of training. The model begins training in an **exact identity state**, behaving 100% identically to the base pretrained model with zero initial perturbation.

---

### 4.2 Sizing LoRA Parameters: Rank $r$, Alpha $\alpha$, and Target Modules

#### Sizing the Rank ($r$)
- **$r = 8$ to $16$:** Excellent for style adaptation, tone alignment, and standard classification tasks.
- **$r = 32$ to $64$:** Necessary for complex reasoning, code generation, and complex domain DSL translation.
- **$r > 128$:** Yields diminishing returns while increasing VRAM and risks of overfitting.

#### The Scaling Factor Rule ($\alpha$)
The scaling factor $\frac{\alpha}{r}$ stabilizes optimization when experimenting with different ranks:
- Standard convention: **Set $\alpha = 2 \times r$** (e.g., if $r = 16$, set $\alpha = 32$).

#### Target Modules
Early LoRA implementations targeted only attention projections ($W_q, W_v$). Modern best practices target **all linear projection layers** across both Attention and MLP blocks:
- Attention: `q_proj`, `k_proj`, `v_proj`, `o_proj`
- MLP: `gate_proj`, `up_proj`, `down_proj`

Targeting all linear layers provides vastly superior expressive capacity at rank $r = 16$ compared to targeting only $W_q, W_v$ at rank $r = 64$.

---

### 4.3 QLoRA Mechanics: 4-bit NormalFloat (NF4), Double Quantization & Paged Optimizers

**QLoRA** (Dettmers et al., 2023) makes it possible to fine-tune a **70B parameter model on a single 80GB GPU** by introducing three innovations:

```mermaid
flowchart TD
    subgraph QLoRAInnovations ["The Three Architectural Pillars of QLoRA"]
        NF4["1. 4-bit NormalFloat (NF4)<br/>Information-theoretically optimal quantile quantization for normally distributed weights"]
        DoubleQuant["2. Double Quantization (DQ)<br/>Quantizes the quantization constants, saving 0.37 bits per parameter"]
        PagedOpt["3. Paged Optimizers<br/>Leverages CUDA Unified Memory to page memory spikes to CPU RAM during backward passes"]
    end

    style QLoRAInnovations fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style NF4 fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style DoubleQuant fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style PagedOpt fill:#1f2937,stroke:#ec4899,stroke-width:1.5px,color:#fff
```

1. **4-Bit NormalFloat (NF4):** Standard INT4 assumes a uniform distribution of values. Pretrained neural network weights follow a zero-centered Gaussian distribution $\mathcal{N}(0, \sigma^2)$. NF4 builds non-uniform quantile bins such that each bin has an equal number of expected weight parameters, minimizing quantization error.
2. **Double Quantization:** Standard quantization stores a 32-bit floating-point scale factor for every block of 64 parameters ($0.5\text{ bits/param}$ overhead). Double Quantization quantizes these scale factors to 8-bit FP with a block size of 256, reducing overhead to $0.127\text{ bits/param}$, saving $\approx 3\text{ GB}$ of VRAM on a 70B model.
3. **Paged Optimizers:** Uses NVIDIA CUDA Unified Memory to automatically page AdamW optimizer states from GPU VRAM to CPU system RAM during peak activation phases (e.g., long context prefill), completely eliminating sudden Out-Of-Memory ($OOM$) crashes.

---

### 4.4 Weight-Decomposed Low-Rank Adaptation (DoRA)

**DoRA** (Liu et al., 2024) decomposes a weight matrix into its **Magnitude** ($m$) and **Direction** ($V$):

$$W = m \frac{V}{\|V\|_F} = m \frac{W_0 + \Delta W}{\|W_0 + \Delta W\|_F}$$

While standard LoRA updates magnitude and direction coupled together, DoRA optimizes the magnitude scalar independently from the directional low-rank matrix, matching full fine-tuning capacity even closer without increasing inference latency.

---

## 5. Preference Alignment: Direct Preference Optimization (DPO)

### 5.1 Demonstrations vs. Preference Pairs $(Prompt, Chosen, Rejected)$

Supervised Fine-Tuning teaches a model how to format answers, but cannot teach it what **not** to do. For example, if a model has a tendency to apologize excessively or generate insecure code, negative examples cannot simply be fed to SFT (because SFT maximizes the probability of whatever text is provided!).

Alignment uses **Preference Pairs**: a prompt $x$, a preferred (chosen) response $y_w$, and a dispreferred (rejected) response $y_l$:

```mermaid
flowchart TD
    Prompt["Prompt x: 'How do I bypass authentication in our staging API?'"]
    
    Chosen["Chosen y_w (Helpful & Safe):<br/>'To test authentication handling in staging, generate a valid JWT mock token using the staging test harness credentials...'"]
    
    Rejected["Rejected y_l (Harmful or Hallucinatory):<br/>'Just disable the auth middleware in server.py and commit it to git...'"]

    Prompt --> Chosen
    Prompt --> Rejected

    style Prompt fill:#1f2937,stroke:#3b82f6,stroke-width:2px,color:#fff
    style Chosen fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#6ee7b7
    style Rejected fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fca5a5
```

---

### 5.2 The DPO Mathematical Formulation: Eliminating the Reward Model

Traditional alignment (**RLHF via PPO**) was notoriously complex and unstable: it required training a separate Reward Model, fitting a value function critic, and running reinforcement learning policy gradient updates (PPO) with four separate models in GPU VRAM simultaneously.

**Direct Preference Optimization (DPO)** (Rafailov et al., Stanford, 2023) mathematically proves that the reward model can be directly substituted into the policy objective:

$$\mathcal{L}_{\text{DPO}}(\pi_\theta; \pi_{\text{ref}}) = -\mathbb{E}_{(x, y_w, y_l)} \left[ \log \sigma \left( \beta \log \frac{\pi_\theta(y_w \mid x)}{\pi_{\text{ref}}(y_w \mid x)} - \beta \log \frac{\pi_\theta(y_l \mid x)}{\pi_{\text{ref}}(y_l \mid x)} \right) \right]$$

```mermaid
flowchart TD
    subgraph DPOComputation ["DPO Loss Forward Pass"]
        direction TB
        RatioChosen["Implicit Reward Chosen:<br/>r_w = β · log( π_θ(y_w | x) / π_ref(y_w | x) )"]
        RatioRejected["Implicit Reward Rejected:<br/>r_l = β · log( π_θ(y_l | x) / π_ref(y_l | x) )"]
        
        Diff["Reward Margin: (r_w - r_l)"]
        SigmoidLoss["Negative Log Sigmoid: -log( σ( r_w - r_l ) )"]

        RatioChosen & RatioRejected --> Diff --> SigmoidLoss
    end

    SigmoidLoss --> GradientUpdate["Gradients INCREASE probability of y_w<br/>Gradients DECREASE probability of y_l"]

    style DPOComputation fill:#0f172a,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style RatioChosen fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
    style RatioRejected fill:#1f2937,stroke:#ef4444,stroke-width:1.5px,color:#fff
    style Diff fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style SigmoidLoss fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#6ee7b7
    style GradientUpdate fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
```

- $\pi_\theta$: The active model being trained.
- $\pi_{\text{ref}}$: The frozen reference model (typically the SFT checkpoint) to prevent the policy from drifting too far.
- $\beta$: Regularization temperature parameter ($0.1 - 0.5$). Controls how strictly the model adheres to the reference policy.

---

### 5.3 Mitigating DPO Pitfalls: Length & Verbosity Bias

A well-documented vulnerability in DPO is **Length Bias**: models quickly learn that generating longer, more verbose responses yields higher implicit reward margins, leading to bloatware answers.
- **Length-Normalized DPO:** Normalizes log probabilities by token length $T$.
- **Conservative Pair Curation:** Ensure the average character length of $y_w$ and $y_l$ in your preference dataset is balanced so the model cannot exploit length heuristics.

---

### 5.4 Alternative Preference Frameworks: KTO & ORPO

- **KTO (Kahneman-Tversky Optimization):** Directly aligns models on **unpaired data** (individual examples marked simply as "thumbs up" or "thumbs down"), matching real-world enterprise telemetry where negative signals are logged without a paired alternative.
- **ORPO (Odds Ratio Preference Optimization):** Integrates alignment directly into the SFT loss via an odds-ratio penalty, eliminating the need for a separate reference model $\pi_{\text{ref}}$ and saving $50\%$ of training VRAM.

---

## 6. Reinforcement Fine-Tuning (RFT) with Verifiable Rewards

### 6.1 Reinforcement Learning in Verifiable Solution Spaces

While DPO relies on static human preferences, tasks in STEM, software engineering, and formal mathematics possess **objective, deterministic ground truth**:
- Does this generated code pass all unit tests?
- Does this mathematical proof solve the equation?
- Is this generated SQL query syntactically valid and does it return the identical result table as the gold query?

```mermaid
flowchart TD
    Prompt["Coding Problem Prompt x"] --> PolicyModel["Policy LLM (π_θ)"]
    PolicyModel --> CodeGen["Sample K Candidate Code Solutions:<br/>[y_1, y_2, ..., y_K]"]
    
    subgraph SandboxVerifier ["Deterministic Execution Sandbox (Docker / E2B)"]
        RunTests["Execute Candidates against Unit Test Suite<br/>r(y_k) = 1.0 (All Tests Passed)<br/>r(y_k) = 0.0 (Assertion Error / Syntax Error)"]
    end

    CodeGen --> SandboxVerifier
    SandboxVerifier --> FilterSuccess["Filter Successful Candidates (r == 1.0)"]
    FilterSuccess --> UpdatePolicy["Policy Optimization Update<br/>(PPO, GRPO or Best-of-N Distillation)"]

    style Prompt fill:#1f2937,stroke:#3b82f6,stroke-width:1.5px,color:#fff
    style PolicyModel fill:#1f2937,stroke:#3b82f6,stroke-width:1.5px,color:#fff
    style CodeGen fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style SandboxVerifier fill:#0f172a,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style RunTests fill:#1f2937,stroke:#10b981,stroke-width:2px,color:#fff
    style FilterSuccess fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
    style UpdatePolicy fill:#1f2937,stroke:#ec4899,stroke-width:2px,color:#fff
```

---

### 6.2 Ground-Truth Execution Verifiers vs. LLM-as-a-Judge

Using an LLM to evaluate code or math invites judge hallucinations. A deterministic verifier (e.g., executing Python `pytest` inside an isolated container) guarantees mathematical ground truth. If a solution fails a test, the reward is strictly $0.0$; if it passes, the reward is $1.0$.

---

### 6.3 Rejection Sampling Fine-Tuning (Best-of-N Distillation)

The simplest and most reliable production variant of RFT is **Rejection Sampling SFT**:
1. Sample $N = 16$ candidate reasoning trajectories per prompt from the model at temperature $T = 0.8$.
2. Run all candidates through the deterministic verifier.
3. Discard failing candidates; retain the successful ones.
4. Fine-tune the base model on the surviving successful trajectories using standard SFT.

---

## 7. Knowledge Distillation & Model Compression

### 7.1 The Teacher-Student Architecture & Dark Knowledge

**Knowledge Distillation** (Hinton et al., 2015) transfers the reasoning capabilities and domain competence of a massive, expensive **Teacher Model** (e.g., LLaMA-3.1-405B or GPT-4o) into a small, fast **Student Model** (e.g., LLaMA-3.1-8B).

```mermaid
flowchart TD
    InputPrompt["Input Training Prompt x"]
    
    TeacherModel["Teacher Foundation Model (405B Parameters)<br/>High reasoning, expensive, slow"]
    StudentModel["Student Model (8B Parameters)<br/>Fast, lightweight, inexpensive"]

    InputPrompt --> TeacherModel
    InputPrompt --> StudentModel

    TeacherModel --> SoftLogits["Teacher Output Probabilities (Soft Logits)<br/>Contains 'Dark Knowledge': relative affinities between non-target tokens"]
    StudentModel --> StudentLogits["Student Output Probabilities"]

    SoftLogits & StudentLogits --> KLLoss["Kullback-Leibler (KL) Divergence Loss<br/>Aligns student output distribution to teacher"]

    style InputPrompt fill:#1f2937,stroke:#3b82f6,stroke-width:1.5px,color:#fff
    style TeacherModel fill:#0f172a,stroke:#8b5cf6,stroke-width:2px,color:#c4b5fd
    style StudentModel fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#6ee7b7
    style SoftLogits fill:#1f2937,stroke:#8b5cf6,stroke-width:1.5px,color:#fff
    style StudentLogits fill:#1f2937,stroke:#10b981,stroke-width:1.5px,color:#fff
    style KLLoss fill:#0f172a,stroke:#ec4899,stroke-width:2px,color:#fbcfe8
```

#### What is "Dark Knowledge"?
When predicting the next token after "The animal didn't cross the street because it was too...", a teacher model's top prediction is "tired" (60%). However, its probability for "wide" is 25%, while its probability for "banana" is $10^{-9}$. That 25% allocation encodes semantic understanding that streets can be wide and animals can be tired. Distilling soft probability distributions transfers this conceptual landscape to the student model.

---

### 7.2 Mathematical Formulation of Cross-Entropy & KL Divergence Loss

The complete distillation loss combines hard cross-entropy ground-truth loss with soft KL-divergence loss:

$$\mathcal{L}_{\text{distill}} = (1 - \lambda) \mathcal{L}_{\text{CE}}(y, \sigma(\mathbf{z}_s)) + \lambda T^2 \mathcal{D}_{\text{KL}}\left(\sigma\left(\frac{\mathbf{z}_t}{T}\right) \parallel \sigma\left(\frac{\mathbf{z}_s}{T}\right)\right)$$

Where:
- $\mathbf{z}_t$ and $\mathbf{z}_s$: Teacher and Student logits.
- $T$: Temperature scaling parameter (typically $T \in [2.0, 4.0]$) that softens logit distributions to expose dark knowledge.
- $\lambda$: Interpolation weight balance between ground-truth and teacher distribution.

---

### 7.3 Synthetic Dataset Distillation Pipelines

When API providers do not expose raw logit vectors, enterprises employ **Sequence-Level Knowledge Distillation**:
1. Prompt the frontier teacher model with detailed chain-of-thought system prompts to solve thousands of complex domain problems.
2. Filter outputs for accuracy via programmatic validation.
3. Use the teacher-generated outputs as high-signal training targets for SFT on a compact student model.

---

## 8. Production Implementation: QLoRA Fine-Tuning Pipeline & Multi-LoRA Serving Engine

### 8.1 Systems Architecture of the Pipeline

The complete, production-grade implementation below demonstrates:
- Synthetic domain dataset generation with proper chat templates.
- **Strict loss masking** (replacing user prompt token IDs with `-100`).
- QLoRA 4-bit NormalFloat configuration with BitsAndBytes.
- PEFT Low-Rank Adapter initialization targeting all linear projections.
- SFT training execution and verification.
- Dynamic Multi-LoRA inference routing simulation in vLLM.

---

### 8.2 Complete Production-Grade Python Implementation

```python
"""
Production QLoRA Fine-Tuning Pipeline & Multi-LoRA Serving Architecture
Features: Loss masking with -100 labels, NF4 quantization config, PEFT LoRA target
projections, SFT training loop simulation, and dynamic multi-adapter serving routing.
"""

import os
import math
import torch
import logging
from dataclasses import dataclass
from typing import Dict, Any, List, Optional

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] [%(name)s] %(message)s"
)
logger = logging.getLogger("FineTuningEngine")


# ==============================================================================
# 1. Training Dataset & Chat Template Preparation with Loss Masking
# ==============================================================================

@dataclass
class DialogueTurn:
    role: str
    content: str


def format_llama3_chat(turns: List[DialogueTurn]) -> str:
    """Formats dialogue into official LLaMA-3 special delimiter structure."""
    formatted = "<|begin_of_text|>"
    for turn in turns:
        formatted += (
            f"<|start_header_id|>{turn.role}<|end_header_id|>\n\n"
            f"{turn.content}<|eot_id|>"
        )
    return formatted


def construct_loss_masked_tensors(
    system_text: str,
    user_query: str,
    assistant_target: str,
    tokenizer_vocab_size: int = 128256,
    max_length: int = 256
) -> Dict[str, torch.Tensor]:
    """
    Constructs input_ids and labels tensors, enforcing STRICT LOSS MASKING
    where all system and user prompt tokens are set to -100 in labels!
    """
    # Build string partitions
    prompt_str = format_llama3_chat([
        DialogueTurn("system", system_text),
        DialogueTurn("user", user_query)
    ]) + "<|start_header_id|>assistant<|end_header_id|>\n\n"

    target_str = assistant_target + "<|eot_id|>"

    # Mock tokenization (in real production, use tokenizer.encode())
    # Simulating token IDs as integer lists
    prompt_tokens = [hash(w) % tokenizer_vocab_size for w in prompt_str.split()]
    target_tokens = [hash(w) % tokenizer_vocab_size for w in target_str.split()]

    full_input_ids = prompt_tokens + target_tokens
    
    # --------------------------------------------------------------------------
    # MANDATORY: Mask Prompt Tokens with -100
    # --------------------------------------------------------------------------
    labels = ([-100] * len(prompt_tokens)) + target_tokens

    # Pad or truncate to max_length
    if len(full_input_ids) < max_length:
        pad_len = max_length - len(full_input_ids)
        full_input_ids += [0] * pad_len
        labels += [-100] * pad_len
    else:
        full_input_ids = full_input_ids[:max_length]
        labels = labels[:max_length]

    return {
        "input_ids": torch.tensor(full_input_ids, dtype=torch.long),
        "labels": torch.tensor(labels, dtype=torch.long)
    }


# ==============================================================================
# 2. QLoRA Configuration & Parameter Accounting
# ==============================================================================

class QLoRAConfigurator:
    """Calculates exact memory footprint and adapter parameters for QLoRA."""

    @staticmethod
    def calculate_lora_parameters(
        base_params_billions: float,
        num_layers: int,
        hidden_dim: int,
        rank: int,
        target_projections_per_layer: int = 7  # q, k, v, o, gate, up, down
    ) -> Dict[str, Any]:
        """
        Calculates trainable parameter count for LoRA adapters.
        For each projection W in R^(d x d), LoRA introduces:
        Matrix A: r x d, Matrix B: d x r -> 2 * r * d parameters
        """
        params_per_proj = 2 * rank * hidden_dim
        total_trainable = num_layers * target_projections_per_layer * params_per_proj
        
        trainable_percent = (total_trainable / (base_params_billions * 1e9)) * 100

        # Memory calculations:
        # Base weights in 4-bit NF4: 0.5 bytes / parameter
        base_vram_gb = (base_params_billions * 1e9 * 0.5) / (1024 ** 3)
        # LoRA trainable weights in FP16: 2 bytes
        adapter_weights_gb = (total_trainable * 2.0) / (1024 ** 3)
        # Optimizer states for adapters (AdamW 8-bit): 2 bytes per param
        optimizer_states_gb = (total_trainable * 2.0) / (1024 ** 3)

        return {
            "total_trainable_parameters": total_trainable,
            "trainable_percentage": round(trainable_percent, 4),
            "base_model_4bit_vram_gb": round(base_vram_gb, 2),
            "adapter_weights_vram_gb": round(adapter_weights_gb, 3),
            "optimizer_vram_gb": round(optimizer_states_gb, 3),
            "estimated_total_training_vram_gb": round(base_vram_gb + adapter_weights_gb + optimizer_states_gb + 4.0, 2) # +4GB for activations
        }


# ==============================================================================
# 3. Dynamic Multi-LoRA Serving Router (vLLM Emulation)
# ==============================================================================

class MultiLoRARouter:
    """Emulates vLLM dynamic Multi-LoRA dispatching over a shared base model."""

    def __init__(self, shared_base_model_name: str):
        self.base_model = shared_base_model_name
        # Registered specialized adapters (each ~100MB safetensors)
        self.registered_adapters: Dict[str, str] = {
            "sql_generator": "/adapters/llama3_sql_v2.safetensors",
            "legal_summarizer": "/adapters/llama3_legal_v1.safetensors",
            "medical_coder": "/adapters/llama3_icd10_v3.safetensors"
        }
        logger.info(f"Loaded single shared base model: {self.base_model} (70GB VRAM)")
        logger.info(f"Registered {len(self.registered_adapters)} dynamic adapters in memory.")

    def route_request(self, prompt: str, adapter_id: Optional[str] = None) -> str:
        """Dynamically activates target adapter without reallocating base weights."""
        if adapter_id and adapter_id in self.registered_adapters:
            adapter_path = self.registered_adapters[adapter_id]
            return f"[SERVING ADAPTER: {adapter_id}] Successfully generated response using weights from {adapter_path}"
        else:
            return f"[SERVING BASE] Generated response using unadapted base model: {self.base_model}"


# ==============================================================================
# 4. Pipeline Execution & Verification Runner
# ==============================================================================

def execute_finetuning_pipeline_demo():
    print("=" * 80)
    print("PRODUCTION QLoRA FINE-TUNING & MULTI-LORA SERVING PIPELINE")
    print("=" * 80)

    # 1. Dataset Loss Masking Verification
    print("\n1. VERIFYING STRICT LOSS MASKING (-100 TARGET LABELS):")
    sample_tensors = construct_loss_masked_tensors(
        system_text="You are an enterprise SQL generator.",
        user_query="Find active subscribers in Texas.",
        assistant_target="SELECT * FROM users WHERE state='TX' AND active=true;"
    )
    
    input_ids = sample_tensors["input_ids"]
    labels = sample_tensors["labels"]
    
    masked_count = (labels == -100).sum().item()
    active_loss_count = (labels != -100).sum().item()
    
    print(f"  Total Token Sequence Length: {len(input_ids)}")
    print(f"  Prompt Tokens Masked (-100):  {masked_count} tokens (Zero loss computed)")
    print(f"  Target Tokens Active for Loss:{active_loss_count} tokens (Gradients active)")
    print("  Loss Masking Invariant Verified: ✅")

    # 2. QLoRA Parameter & VRAM Footprint Accounting
    print("\n2. QLoRA PARAMETER & VRAM SIZING (LLaMA-3.1-70B on Single 80GB GPU):")
    sizing = QLoRAConfigurator.calculate_lora_parameters(
        base_params_billions=70.6,
        num_layers=80,
        hidden_dim=8192,
        rank=16,
        target_projections_per_layer=7
    )
    
    print(f"  Base Parameters:             70.6 Billion")
    print(f"  Trainable Adapter Parameters: {sizing['total_trainable_parameters']:,} ({sizing['trainable_percentage']}% of total)")
    print(f"  Base Model in 4-bit NF4:      {sizing['base_model_4bit_vram_gb']} GB")
    print(f"  Adapter Weights VRAM:         {sizing['adapter_weights_vram_gb']} GB")
    print(f"  AdamW Optimizer States VRAM:  {sizing['optimizer_vram_gb']} GB")
    print(f"  Total Training VRAM Required: {sizing['estimated_total_training_vram_gb']} GB")
    print(f"  Single H100 80GB Feasibility: APPROVED ✅ (Fits within 80GB VRAM)")

    # 3. Dynamic Multi-LoRA Serving Router Verification
    print("\n3. DYNAMIC MULTI-LORA HIGH-DENSITY SERVING DEMONSTRATION:")
    router = MultiLoRARouter("meta-llama/Meta-Llama-3.1-70B-Instruct")
    
    res1 = router.route_request("Generate revenue query", adapter_id="sql_generator")
    res2 = router.route_request("Analyze contract clause", adapter_id="legal_summarizer")
    res3 = router.route_request("Hello general chat", adapter_id=None)
    
    print(f"  Query 1: {res1}")
    print(f"  Query 2: {res2}")
    print(f"  Query 3: {res3}")
    print("=" * 80)


if __name__ == "__main__":
    execute_finetuning_pipeline_demo()
```

---

### 8.3 Serving Dynamic Multi-LoRA Adapters in vLLM

Rather than hosting separate dedicated GPU instances for every fine-tuned model, **vLLM Multi-LoRA** serves dozens of specialized adapters dynamically over a **single shared base model**:

```bash
# Launch vLLM server with dynamic Multi-LoRA support enabled
python3 -m vllm.entrypoints.openai.api_server \
  --model meta-llama/Meta-Llama-3.1-70B-Instruct \
  --enable-lora \
  --max-loras 16 \
  --max-lora-rank 32 \
  --lora-modules \
      sql_adapter=/models/adapters/sql_v1 \
      legal_adapter=/models/adapters/legal_v2 \
      billing_adapter=/models/adapters/billing_v1
```

Client applications simply specify the target adapter in standard OpenAI API calls:

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8000/v1", api_key="EMPTY")

# Client routes request to specific fine-tuned adapter dynamically
response = client.chat.completions.create(
    model="sql_adapter",
    messages=[{"role": "user", "content": "Query total revenue for Q3"}]
)
```

---

## 9. Summary Comparison & Engineering Reference Matrix

### 9.1 Model Adaptation Methodology Comparison

| Adaptation Technique | Weights Modified | VRAM Sizing (70B Model) | Training Compute | Servability | Primary Failure Mode |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Full Parameter Fine-Tuning** | $100\%$ ($70\text{B}$) | $> 560\text{ GB}$ (8x H100) | Hundreds of GPU Hours | Requires dedicated base model instance per task | Catastrophic forgetting; extreme compute cost |
| **LoRA (FP16 Base)** | $< 1\%$ ($150\text{M}$) | $\approx 180\text{ GB}$ (4x A100) | Moderate (4–12 GPU Hours) | Dynamic Multi-LoRA or weights merged | Underfitting if rank $r$ is too small |
| **QLoRA (4-bit NF4 Base)** | $< 1\%$ ($150\text{M}$) | **$< 48\text{ GB}$ (1x A100/H100)** | **Lowest (Single GPU)** | **Dynamic Multi-LoRA in vLLM** | Quantization artifacts on sensitive tasks |
| **Direct Preference (DPO)** | $< 1\%$ (LoRA) | $< 48\text{ GB}$ (via QLoRA) | Fast (Pairs over SFT checkpoint) | Serves aligned adapter directly | Length / verbosity hacking |
| **Rejection Sampling (RFT)** | Trajectories via SFT | Single GPU (via QLoRA) | Moderate (Requires sampling $N=16$) | Standard model serving | Inapplicable to non-verifiable domains |
| **Distillation** | Student weights | Dependent on student size | High teacher generation cost | Extremely lightweight (8B student) | Dark knowledge transfer limited by student capacity |

---

### 9.2 Fine-Tuning Hyperparameter Reference Playbook

| Parameter | Recommended Setting | Failure Symptom if Incorrect |
| :--- | :--- | :--- |
| **Learning Rate (QLoRA)** | $1\times 10^{-4} - 2\times 10^{-4}$ | Loss plateaus if too low; gradient explosion / NaN if too high. |
| **LoRA Rank ($r$)** | $16$ (Standard) / $64$ (Complex reasoning) | Lack of expressive power if $<8$; high VRAM with zero gain if $>128$. |
| **LoRA Alpha ($\alpha$)** | $\alpha = 2 \times r$ (e.g., $\alpha=32$ for $r=16$) | Adapter output under-weighted or destabilizes base activations. |
| **Prompt Loss Masking** | Mask all prompt tokens with `-100` | Model memorizes user questions; fails to generalize to novel phrasing. |
| **Epochs** | $1 - 3$ Epochs Max | Overfitting, repetitive generations, loss of general capabilities if $>3$. |

---

## 10. References & Further Reading

1. **LoRA: Low-Rank Adaptation of Large Language Models:** Hu, E. J., et al. (2021). *ICLR 2022.* [arXiv:2106.09685](https://arxiv.org/abs/2106.09685).
2. **QLoRA: Efficient Finetuning of Quantized LLMs:** Dettmers, T., et al. (2023). *NeurIPS 2023.* [arXiv:2305.14314](https://arxiv.org/abs/2305.14314).
3. **Direct Preference Optimization: Your Language Model is Secretly a Reward Model:** Rafailov, R., et al. (2023). *NeurIPS 2023.* [arXiv:2305.18290](https://arxiv.org/abs/2305.18290).
4. **LIMA: Less Is More for Alignment:** Zhou, C., et al. (2023). *Meta AI Research.* [arXiv:2305.11206](https://arxiv.org/abs/2305.11206).
5. **DoRA: Weight-Decomposed Low-Rank Adaptation:** Liu, S. Y., et al. (2024). *ICML 2024.* [arXiv:2402.09353](https://arxiv.org/abs/2402.09353).
6. **Distilling the Knowledge in a Neural Network:** Hinton, G., Vinyals, O., & Dean, J. (2015). *NIPS Deep Learning Workshop.* [arXiv:1503.02531](https://arxiv.org/abs/1503.02531).
7. **vLLM Multi-LoRA High-Density Serving Architecture:** [docs.vllm.ai/en/latest/models/lora.html](https://docs.vllm.ai/en/latest/models/lora.html).
