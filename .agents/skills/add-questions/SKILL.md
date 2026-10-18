---
name: add-questions
description: >-
  Use this skill whenever a new questions file (e.g. `questions-dump.md`, `question_dump.md`,
  `*_question*.md`, `*_questions*.md`, or similar) is created or detected under a
  `frequently_asked_questions/` folder. Analyzes, deduplicates, and integrates interview questions.
  Enforces underlying base topic presence in core curriculum modules (strictly 01_*.md to 10_*.md) by invoking
  `add-topics` if missing, cataloging in `00_<track>_faq_study_guide_and_index.md`, authoring deep-dive
  solutions in `NN_*_faq.md` (strictly 1:1 matching base topics), linking base topics, updating indexes with legible wrapped tables, and
  deleting the source questions file upon completion.
---

# Add Questions Skill

This skill defines the authoritative, production-grade operational protocol for discovering, auditing, answering, cross-linking, and indexing interview questions from any question dump or staging file (e.g., `questions-dump.md`, `question_dump.md`, `*_question*.md`, `*_questions*.md`, `new_questions.md`) placed inside a `frequently_asked_questions/` directory of any engineering track (e.g., `Java Backend Engineering/frequently_asked_questions/`, `Data Engineering/frequently_asked_questions/`, `AI Engineering/frequently_asked_questions/`, etc.).

It strictly enforces that every question is grounded in an underlying base topic documented in the core curriculum modules (strictly `01_` through `10_`), is cataloged in the track's master FAQ study guide and question bank (`00_<track>_faq_study_guide_and_index.md`), is authored with staff-level rigor in the scenario FAQ modules adhering to `AGENTS.md` (maintaining strict 1:1 numeric parity with the core curriculum topics), and is bidirectionally linked with topic-to-FAQ links. Separate raw question files (`*_interview_questions.md`) are not created or maintained.

---

## Architectural Workflow: Questions Ingestion Lifecycle

```mermaid
flowchart TD
    Trigger["📄 New Questions File in FAQ Folder<br/>(questions-dump.md, *_questions*.md, etc.)"] --> Parse["1. Parse & Categorize Questions"]
    Parse --> Dedupe["2. Audit Against Existing FAQs & Question Bank"]
    Dedupe --> CheckCovered{"Question Already Answered?"}
    CheckCovered -- "Yes" --> SkipQ["Log as already covered"]
    CheckCovered -- "No" --> CheckBase{"3. Is Underlying Base Topic in Core Modules?"}

    CheckBase -- "No (Missing)" --> TriggerTopic["3a. Invoke add-topics Skill<br/>(Author Base Topic in Core Module 01-10)"]
    TriggerTopic --> LinkAnchor["Obtain Anchor to Core Topic"]
    CheckBase -- "Yes" --> LinkAnchor

    LinkAnchor --> CatalogQuestion["4. Catalog Question in 00_*_faq_study_guide_and_index.md"]
    CatalogQuestion --> SelectFAQ["5. Select 1:1 Paired FAQ Module (01 to 10)"]
    SelectFAQ --> AuthorAnswer["6. Author Deep-Dive Answer (Section 4 Blueprint)"]
    AuthorAnswer --> UpdateFAQTOC["7. Update FAQ Module TOC & Numbering"]
    UpdateFAQTOC --> SyncAnchor["8. Synchronize Solution Anchor & Update Index Table"]
    SyncAnchor --> UpdateBaseTopicLink["9. Update Topic-to-FAQ Reference in Base Module (NN_*.md)"]
    SkipQ --> CompleteCheck{"All Dump Questions Processed?"}
    UpdateBaseTopicLink --> CompleteCheck
    CompleteCheck -- "Yes" --> DeleteDump["10. Delete Source Questions File & Verify Links"]

    style Trigger fill:#0f172a,stroke:#3b82f6,stroke-width:2px,color:#93c5fd
    style Parse fill:#1e293b,stroke:#64748b,stroke-width:1.5px,color:#f8fafc
    style Dedupe fill:#1e293b,stroke:#64748b,stroke-width:1.5px,color:#f8fafc
    style CheckCovered fill:#1f2937,stroke:#f59e0b,stroke-width:2px,color:#fbbf24
    style CheckBase fill:#1f2937,stroke:#ec4899,stroke-width:2px,color:#f472b6
    style TriggerTopic fill:#0f172a,stroke:#ec4899,stroke-width:2px,color:#f472b6
    style CatalogQuestion fill:#1e293b,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style SelectFAQ fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#6ee7b7
    style AuthorAnswer fill:#0f172a,stroke:#10b981,stroke-width:2px,color:#6ee7b7
    style UpdateFAQTOC fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#ffffff
    style SyncAnchor fill:#1e293b,stroke:#10b981,stroke-width:1.5px,color:#ffffff
    style UpdateBaseTopicLink fill:#1e293b,stroke:#3b82f6,stroke-width:1.5px,color:#93c5fd
    style DeleteDump fill:#1f2937,stroke:#ef4444,stroke-width:2px,color:#fca5a5
```

---

## Table of Contents
1. [Trigger Conditions & Flexible Filename Matching](#1-trigger-conditions-flexible-filename-matching)
2. [Step-by-Step Execution Protocol](#2-step-by-step-execution-protocol)
   - [Step 1: Ingestion & Question Decomposition](#step-1-ingestion-question-decomposition)
   - [Step 2: Deduplication & Coverage Audit](#step-2-deduplication-coverage-audit)
   - [Step 3: Base Topic Verification & Ingestion (Prerequisite Enforcement)](#step-3-base-topic-verification-ingestion-prerequisite-enforcement)
   - [Step 4: Master FAQ Study Guide & Question Bank Cataloging](#step-4-master-faq-study-guide-question-bank-cataloging)
   - [Step 5: FAQ Module Selection & Placement](#step-5-faq-module-selection-placement-11-pairing-mandate)
   - [Step 6: Deep-Dive Solution Authoring (Section 4 Blueprint)](#step-6-deep-dive-solution-authoring-section-4-blueprint)
   - [Step 7: Table of Contents & Index Synchronization](#step-7-table-of-contents-index-topic-to-faq-link-synchronization)
   - [Step 8: Post-Ingestion Verification & Cleanup](#step-8-post-ingestion-verification-cleanup)
3. [Summary Comparison & Engineering Reference Matrix](#3-summary-comparison-engineering-reference-matrix)
4. [Quality Assurance Checklist](#4-quality-assurance-checklist)

---

## 1. Trigger Conditions & Flexible Filename Matching

Activate this skill when:
- A new file matching any of the following patterns is created or detected under a `frequently_asked_questions/` directory:
  - `questions-dump.md`, `questions_dump.md`, `question-dump.md`, `question_dump.md`
  - `*_question.md`, `*_questions.md`, `*question*.md`, `*questions*.md`
  - `new_questions.md`, `add_questions.md`, `raw_questions.md`
- A user prompts to "add questions", "integrate questions dump", or "process questions file".

> [!CAUTION]
> **Permanent Asset Protection Rule:**
> The master study guide & question bank `00_<track_slug>_faq_study_guide_and_index.md` and numbered scenario modules `01_*_faq.md` through `10_*_faq.md` are permanent repository assets.
> Never treat `00_<track_slug>_faq_study_guide_and_index.md` as a staging or dump file! Separate `*_interview_questions.md` files are not created; questions are cataloged directly within the FAQ study guide.

---

## 2. Step-by-Step Execution Protocol

### Step 1: Ingestion & Question Decomposition
1. Locate and read the detected questions file in `frequently_asked_questions/`.
2. Extract each individual question:
   - Full verbatim question text and scenario context.
   - Conceptual domain and technical sub-discipline.
   - Low-level engine mechanism tested (e.g., MVCC conflict, lock striping, vector distance metrics, rebalance storm).

### Step 2: Deduplication & Coverage Audit
1. Audit `00_<track_slug>_faq_study_guide_and_index.md` (check the question bank sections for semantic duplicates).
2. Audit the scenario modules: `01_*_faq.md` through `10_*_faq.md`.
3. If already comprehensively covered, mark satisfied and skip duplicate authoring.

### Step 3: Base Topic Verification & Ingestion (Prerequisite Enforcement)

> [!IMPORTANT]
> **Base Topic Prerequisite Rule:**
> An interview question must never exist without its theoretical foundation in the core curriculum. If the underlying mechanism is not documented in Modules `01_` through `10_`, the base topic MUST be added to the core curriculum first.

1. **Verify Core Curriculum Presence:**
   - Search the track's master curriculum guide (`00_<track_slug>_study_guide_and_index.md`) and modules `01_` through `10_`.
2. **If Underlying Base Topic is Missing:**
   - Invoke the `add-topics` skill protocol.
   - Determine which core module (`01_`–`10_`) the topic belongs to.
   - Insert the topic into the appropriate section (`# 1.` to `# 8.`) following the **Zero-Append Mandate**.
   - Provide dual-audience depth, dark-mode Mermaid diagram, and production code.
   - Update that core module's TOC and `# 9. Summary Comparison & Engineering Reference Matrix`.
   - Update `00_<track_slug>_study_guide_and_index.md`.
3. **Capture Base Topic Link:**
   - Record the anchor link: `../<module_file>.md#<topic-anchor>`.

### Step 4: Master FAQ Study Guide & Question Bank Cataloging
1. Open the master FAQ study guide: `00_<track_slug>_faq_study_guide_and_index.md`.
2. Locate the matching topic module section (e.g., `## 01 – ...`).
3. Add the question entry with its difficulty tier badge:
   `N. **`[Easy/Medium/Hard/Expert]`** <Question Text> → [Solution](<target_faq_file>.md#<anchor>)`
4. Update the difficulty distribution counts and total questions in the `Master Topic Directory & Difficulty Matrix` table.

### Step 5: FAQ Module Selection & Placement (1:1 Pairing Mandate)
1. Identify the target scenario FAQ module: `01_*_faq.md` through `10_*_faq.md` matching the base topic module's numeric prefix 1:1 (e.g., Topic 02 pairs strictly with FAQ 02).
2. Locate the matching thematic cluster in the FAQ file.
3. Determine question number `Q<N>` and position within that cluster.

### Step 6: Deep-Dive Solution Authoring (Section 4 Blueprint)
Author each question solution adhering strictly to `AGENTS.md` Section 4:

```markdown
### Q<N>. <Question Title>
*(If questions are grouped: Grouped together: <Reason for grouping>)*  
**Topic Covered:** <Domain & Core Mechanism>  
**Question:** <Full verbatim question>

#### Definition & Core Concepts
<In-depth theoretical explanation of how the engine/runtime processes this under the hood. Dual-audience depth: clear conceptual mental model for juniors, memory layout and kernel syscalls for staff engineers.>

#### Visual Flow
<Mermaid flowchart, sequenceDiagram, or stateDiagram-v2 depicting state flow, failure mode, or lifecycle in Dark Mode palette. Strictly ZERO ASCII.>

#### Production Implementation / Code Solution
```<language>
// Production code with complete syntax, concurrency guards, and line-by-line inline comments
```
- **Time Complexity:** $O(...) $
- **Space Complexity:** $O(...) $

#### Engineering Implications & Production Pitfalls
1. **Anti-Pattern / Hazard:** Why naive solutions fail in high-throughput production (e.g., false sharing, starvation, connection leak).
2. **Interview Talking Point:** Staff-level trade-off nuance demonstrating production experience.

#### Core Curriculum Cross-Reference
*Deep-dive theoretical reference: See [<Module Name>: <Topic Name>](../<module_file>.md#<anchor>)*
```

### Step 7: Table of Contents, Index & Topic-to-FAQ Link Synchronization
1. **FAQ Module TOC:**
   - Update `## Table of Contents & Question Mapping` in the edited `NN_*_faq.md` with the new question title and verified anchor.
2. **Master FAQ Study Guide Synchronization (`00_<track_slug>_faq_study_guide_and_index.md`):**
   - Confirm that the question entry in the question bank section has the exact matching anchor link to the authored solution in `NN_*_faq.md`.
   - Update `## Topic Guides & Coverage Overview` table.
   - **Proportional Table Formatting Enforcement:** Keep row height and width balanced (~3–4 lines tall). Wrap long filenames into at most 2 balanced lines, keep short filenames on 1 line, use single `<br/>` for multiple base concepts, and organize `Key Topics & Grouped Scenarios` into 2–3 structured thematic bullet points (`• **Theme:** ...`) rather than a run-on block of text.
3. **Master Track Index (`00_<track_slug>_study_guide_and_index.md`):**
   - If a new base topic was authored in Step 3, confirm `00_<track_slug>_study_guide_and_index.md` has the new topic in its coverage table.
4. **Base Module Topic-to-FAQ Link (`../NN_*.md`):**
   - Locate the companion base topic module `../NN_<slug>.md`.
   - In the specific section or subsection covering this topic, embed a direct markdown link immediately after the topic content using a dedicated GitHub alert block:
     ```markdown
     > [!TIP]
     > **Deep-Dive Interview Scenario(s):**
     > - [Q<N>. <Question Title>](frequently_asked_questions/NN_<slug>_faq.md#q<n>-<anchor>)
     ```
   - Never group FAQ links into a trailing section at the end of the file; place each link directly adjacent to its corresponding topic.

### Step 8: Post-Ingestion Verification & Cleanup
1. **Integrity Validation:**
   - Confirm all anchors resolve correctly.
   - Confirm cross-module link to the base topic in `../NN_*.md` is valid.
   - Confirm base topic's FAQ reference links back to this question.
   - Confirm Mermaid syntax and Dark-Mode styling (`fill:#0f172a,stroke:#3b82f6,...`).
   - Confirm Zero-ASCII compliance.
2. **Automatic Source File Deletion:**
   - Permanently delete the source staging/dump file from `frequently_asked_questions/` (never touching `00_<track_slug>_faq_study_guide_and_index.md` or any scenario module).
3. **Summary Notification:**
   - Report processed questions, base topics verified/added, modified files, and updated anchors to the user.

---

## 3. Summary Comparison & Engineering Reference Matrix

| Step | Strict Invariant | Failure Mode to Avoid |
| :--- | :--- | :--- |
| **Source Detection** | Support flexible naming (`*_questions*.md`, `questions-dump.md`, etc.) | Rigidly searching for only one hardcoded filename |
| **Asset Protection** | Never delete or overwrite `00_<track>_faq_study_guide_and_index.md` | Treating master study guide as a dump file |
| **Unified Backlog** | Catalog questions directly in `00_<track>_faq_study_guide_and_index.md` | Creating separate standalone `*_interview_questions.md` files |
| **1:1 Mapping** | FAQ file numeric prefix strictly matches base topic prefix (`01` to `10`) | Mismatched file numbering across folders |
| **Base Topic Check** | Core curriculum module MUST cover the underlying topic first | Answering an interview question whose base concept is missing from core modules |
| **FAQ Module Blueprint** | Follow Section 4 structure with dark Mermaid & production code | Giving two-line brief answers or skipping diagrams |
| **Bidirectional Links** | Link question to base topic, and base topic to FAQ question | One-way or broken links between topics and FAQs |
| **Table Proportions** | Balance row height (~3–4 lines), wrap links to max 2 lines, use thematic bullets | 10-line vertical towers or 1,000-char unformatted run-on text |
| **Index Synchronization** | Update both `00_*_faq_study_guide_and_index.md` and TOC | Stale or unmapped question entries |
| **Cleanup** | Automatically delete the source questions file | Leaving processed dump files behind |

---

## 4. Quality Assurance Checklist

- [ ] Source questions file detected and parsed.
- [ ] Confirmed source file is NOT `00_<track_slug>_faq_study_guide_and_index.md` or a scenario module.
- [ ] Question analyzed for core mechanical invariant.
- [ ] Base topic verified in core curriculum module (`01_`–`10_`) (authored via `add-topics` if missing).
- [ ] Question cataloged directly in `00_<track_slug>_faq_study_guide_and_index.md` under its topic section with difficulty badge.
- [ ] Placed in correct thematic cluster in `NN_*_faq.md` (strictly matching base topic number 1:1).
- [ ] Exhaustive `Definition & Core Concepts` explaining low-level engine internals.
- [ ] Dark-mode Mermaid diagram included (Zero-ASCII rule strictly enforced).
- [ ] Production-grade code snippet with line-by-line comments (no synthetic stubs).
- [ ] Explicit $O(...)$ time and space complexity stated.
- [ ] Engineering implications and staff-level interview talking points provided.
- [ ] Clickable link to the base topic in the core curriculum module.
- [ ] Base topic module (`../NN_*.md`) updated with direct link to the new FAQ question in its closing FAQ reference section.
- [ ] FAQ file TOC updated.
- [ ] `00_<track_slug>_faq_study_guide_and_index.md` synchronized and table columns cleanly wrapped for legibility.
- [ ] Source questions file deleted after verified completion.
