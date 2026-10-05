# Interview Preparation & Engineering Knowledge Base

A structured collection of production-grade study guides, architectural deep dives, and real-world interview solutions across three core engineering disciplines: **AI Engineering**, **Data Engineering**, and **Java Backend Systems**.

---

## Tracks Overview

### 1. [AI Engineering](AI%20Engineering/00_ai_study_guide_and_index.md)
Comprehensive guides covering applied machine learning, generative AI, vector retrieval, autonomous agent architectures, and LLM serving infrastructure.

- [01. Machine Learning & MLOps](AI%20Engineering/01_machine_learning_and_ai_engineering.md) — Supervised/unsupervised learning, XGBoost, feature stores, model drift, and MLflow.
- [02. Generative AI, RAG & Vector Search](AI%20Engineering/02_generative_ai_rag_and_vector_search.md) — Transformers, embeddings, HNSW vector indexing, hybrid search, and RAG evaluation.
- [03. AI Agents & Multi-Agent Systems](AI%20Engineering/03_ai_agents_and_multi_agent_systems.md) — ReAct cognitive loops, tool calling, memory management, and LangGraph orchestration.
- [04. LLMOps, Safety & AI Infrastructure](AI%20Engineering/04_llmops_safety_and_ai_infrastructure.md) — vLLM inference engine, PagedAttention, KV cache sizing, and AI safety guardrails.
- [05. AI System Design](AI%20Engineering/05_ai_system_design.md) — Scalable RAG platforms, high-throughput serving architectures, and design interview frameworks.

---

### 2. [Data Engineering](Data%20Engineering/00_de_study_guide_and_index.md)
In-depth coverage of distributed data platforms, database internals, batch and stream processing, Lakehouse ACID engines, orchestration, and enterprise system design.

- **Core Curriculum ([Study Guide & Index](Data%20Engineering/00_de_study_guide_and_index.md))**:
  - Relational & analytical query mastery: [SQL Fundamentals](Data%20Engineering/01_sql_fundamentals.md) | [Advanced SQL](Data%20Engineering/02_advanced_sql.md)
  - Systems & storage engines: [Python for Data Engineering](Data%20Engineering/03_python_for_data_engineering.md) | [Database Internals](Data%20Engineering/04_database_internals_and_performance.md) | [Distributed Systems & APIs](Data%20Engineering/05_distributed_systems_and_apis.md)
  - Architecture & Lakehouse: [Data Modeling](Data%20Engineering/06_data_architecture_and_modeling.md) | [File Formats & Storage](Data%20Engineering/07_file_formats_and_storage.md) | [Apache Spark & PySpark](Data%20Engineering/08_apache_spark_pyspark.md) | [Databricks & Delta Lake](Data%20Engineering/09_databricks_and_delta_lake.md)
  - Ingestion, streaming & operations: [Pipeline Design](Data%20Engineering/10_data_pipeline_design_and_ingestion.md) | [Kafka & Streaming](Data%20Engineering/11_streaming_and_kafka_event_streaming.md) | [Airflow & dbt](Data%20Engineering/12_orchestration_airflow_and_dbt.md) | [Azure & DevOps](Data%20Engineering/13_azure_data_engineering_and_devops.md) | [Governance & Security](Data%20Engineering/14_data_governance_security_and_observability.md) | [System Design](Data%20Engineering/15_system_design_for_data_engineering.md)
- **[Interview FAQs & Scenarios](Data%20Engineering/frequently_asked_questions/00_de_faq_study_guide_and_index.md)**: 10 dedicated scenario guides covering real-world SQL puzzles, Spark performance tuning, Delta Lake internals, streaming watermarks, and pipeline troubleshooting.

---

### 3. [Java Backend Engineering](Java%20Backend/00_java_study_guide_and_index.md)
Deep architectural exploration of JVM mechanics, core Java, modern functional features (Java 8–21), high concurrency, Spring Boot internals, microservices, and cloud operations.

- **Core Curriculum ([Study Guide & Index](Java%20Backend/00_java_study_guide_and_index.md))**:
  - Language & runtime foundations: [Core Java & OOP](Java%20Backend/01_core_java_and_oop.md) | [Collections & Generics](Java%20Backend/02_java_collections_generics_data_handling.md) | [Modern Java (Records & Streams)](Java%20Backend/03_modern_java_records_sealed_streams_functional.md) | [Concurrency & Multithreading](Java%20Backend/04_java_concurrency_multithreading.md) | [JVM Memory & Runtime](Java%20Backend/05_jvm_memory_io_reflection_runtime.md)
  - Architecture & Spring platform: [Design Patterns & SOLID](Java%20Backend/06_design_patterns_solid_software_architecture.md) | [Spring Boot Platform](Java%20Backend/07_spring_framework_spring_boot.md) | [Spring Web & REST APIs](Java%20Backend/08_spring_web_rest_apis.md) | [Spring Security & OAuth2](Java%20Backend/11_security_identity_certificate_management.md)
  - Persistence, messaging & infrastructure: [Databases & SQL](Java%20Backend/09_database_data_engineering.md) | [JPA & Hibernate](Java%20Backend/10_spring_data_jdbc_jpa_hibernate_transactions.md) | [Caching & Kafka](Java%20Backend/12_caching_redis_messaging_kafka_event_driven.md) | [Microservices & System Design](Java%20Backend/13_microservices_distributed_systems_system_design.md) | [Testing & Observability](Java%20Backend/14_testing_observability_performance_production_engineering.md) | [Docker, K8s & Cloud](Java%20Backend/15_docker_kubernetes_cicd_cloud.md)
- **[Interview FAQs & Scenarios](Java%20Backend/frequently_asked_questions/00_java_faq_study_guide_and_index.md)**: 12 dedicated scenario guides covering HashMap internals, multithreading puzzles, Spring bean lifecycle, JPA N+1 optimization, Kafka consumer lag, and thread dump triage.

---

## Directory Structure

```text
.
├── AI Engineering/                 # Machine learning, RAG, agents & LLMOps
├── Data Engineering/               # Distributed data systems, Spark, SQL & Lakehouse
│   └── frequently_asked_questions/ # Real-world data engineering interview scenarios
├── Java Backend/                   # Core Java, Spring Boot, microservices & JVM
│   └── frequently_asked_questions/ # Real-world Java backend interview scenarios
└── README.md                       # Repository introduction and index
```

---

## How to Use This Repository

1. **Pick Your Focus Track**: Jump directly into the track corresponding to your specialization ([AI](AI%20Engineering/00_ai_study_guide_and_index.md), [Data](Data%20Engineering/00_de_study_guide_and_index.md), or [Java Backend](Java%20Backend/00_java_study_guide_and_index.md)).
2. **Follow the Numbered Progression**: Each folder contains numbered modules (`01_...` to `15_...`) designed to be read in logical study order.
3. **Practice with Real Interview Questions**: Use the `frequently_asked_questions/` directories within the Data Engineering and Java Backend folders for scenario-based practice questions with end-to-end solutions.
