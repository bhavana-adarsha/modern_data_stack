# Modern Data Stack: Applied Concepts & Tooling Fluency

## Context

This document captures hands-on engagement with modern data stack tooling and patterns in the context of AI product development — specifically how data infrastructure decisions shape LLM product quality, reliability, and observability.

---

## Data Modeling with dbt

Applied dbt (data build tool) concepts to structure raw scheduling and clinical staffing data into clean, analytics-ready semantic layers.

**Key patterns used:**
- Staging models to normalize raw source tables (shift records, employee availability, time-off requests) into consistent schemas
- Intermediate models to join and enrich data across entities (worker profiles, schedule assignments, unit constraints)
- Mart models to produce flattened, denormalized views optimized for downstream AI feature consumption and reporting
- Column-level documentation and `schema.yml` contracts to enforce data expectations and support lineage tracking

**Why it mattered for AI:**
Grounding LLM-powered features in clean, well-modeled data reduced hallucination risk and made RAG retrieval more precise. A scheduling anomaly agent that queries a validated semantic layer produces more reliable explanations than one operating on raw, inconsistent source data.

---

## Pipeline Orchestration Concepts (Airflow/Dagster patterns)

Worked with DAG-based orchestration patterns to manage dependencies between data ingestion, transformation, and model inference jobs.

**Concepts applied:**
- Separating extraction, transformation, and serving concerns into discrete, retryable pipeline stages
- Designing idempotent tasks that can be safely re-run without producing duplicate or inconsistent outputs
- Triggering downstream model inference jobs only after upstream data quality checks pass
- Alerting on pipeline failures before they silently degrade model input quality

**Product relevance:**
Many LLM product failures are upstream data failures misdiagnosed as model failures. Owning the pipeline layer — even conceptually — allows a PM to define data SLAs, set alerting thresholds, and establish clear ownership boundaries between data engineering and ML teams.

---

## Cloud Data Warehouse Patterns (Snowflake / PostgreSQL)

Hands-on experience with multi-tenant PostgreSQL deployed on Kubernetes, with working knowledge of Snowflake architecture patterns including virtual warehouses, zero-copy cloning, and time-travel for data recovery.

**Applied in practice:**
- Designed per-tenant schema isolation to meet HIPAA data segregation requirements in a SaaS scheduling platform
- Defined query patterns for feature extraction used in AI model training (shift preference vectors, coverage gap signals, anomaly indicators)
- Evaluated trade-offs between row-level security vs. schema-per-tenant vs. database-per-tenant isolation models

**Snowflake-specific awareness:**
- Virtual warehouse sizing trade-offs (compute cost vs. query concurrency)
- Separation of storage and compute enabling independent scaling of transformation jobs and serving queries
- Use of `CLONE` for cost-effective test environment provisioning without data duplication

---

## Data Quality & Observability

Established data quality standards as a prerequisite for AI product reliability — treating data contracts as a first-class product concern.

**Patterns implemented or designed:**
- Defined `not_null`, `unique`, and `accepted_values` tests in dbt to catch schema drift before it reaches model inputs
- Designed alerting on feature distribution shift as an early signal for model degradation (before drift becomes visible in output quality)
- Established data freshness SLAs — defining maximum acceptable lag between source system updates and model-serving layer

**Framing for stakeholders:**
Communicated data quality issues in business terms: a stale time-off feed means the AI schedule is built on incorrect availability data, producing a schedule healthcare workers reject — which directly undermines the product's core value proposition.

---

## Tooling Reference

| Layer | Tool / Pattern | Familiarity |
|---|---|---|
| Transformation | dbt Core | Hands-on |
| Warehouse | PostgreSQL (multi-tenant) | Hands-on |
| Warehouse | Snowflake | Conceptual + architecture |
| Orchestration | Airflow / Dagster patterns | Conceptual |
| Compute | AWS Lambda | Hands-on |
| Infra | Kubernetes (AWS EKS) | Requirements + design |
| Observability | dbt tests, pipeline alerting | Hands-on |
| Feature serving | Custom GraphQL API layer | Hands-on |
