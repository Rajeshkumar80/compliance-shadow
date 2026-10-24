# Compliance Shadow — Technical Architecture

## 1. Architecture objective

Build an evidence-first regulatory intelligence system. Every finding must be traceable to an official source document, a specific version and a clause/page reference.

## 2. Logical architecture

```mermaid
flowchart TB
    SRC["Official regulatory portals"] --> ING["Source ingestion service"]
    ING --> RAW["Raw document archive + hashes"]
    RAW --> PARSE["PDF / HTML extraction"]
    PARSE --> MODEL["DL document-understanding model"]
    MODEL --> DELTA["Document version diff + candidate changes"]
    DELTA --> VAL["Schema, evidence and quality validation"]
    VAL --> DB[("SAP HANA Cloud")]
    BIZ["Organisation / products / shipments"] --> DB
    DB --> RET["HANA retrieval layer"]
    RET --> RAG["RAG context builder"]
    DB --> RULE["Deterministic applicability rules"]
    RAG --> ORCH["LangGraph orchestration"]
    RULE --> ORCH
    ORCH --> API["FastAPI"]
    API --> WEB["React + TypeScript"]
    WEB --> HUMAN["Human review"]
    HUMAN --> AUDIT["Decision and audit history"]
    AUDIT --> DB
    MLOPS["DVC + MLflow + CI evaluation"] -.-> MODEL
    MLOPS -.-> ORCH
```

## 3. Service responsibilities

| Service | Responsibility | Key controls |
|---|---|---|
| Source collector | Fetch permitted official publications | Rate limits, source allowlist, retries |
| Document archive | Preserve original document and metadata | Hash, immutable version record |
| Extraction service | Extract text, tables, pages and layout | OCR fallback, extraction quality flags |
| DL service | Extract structured regulatory fields | Versioned model, confidence, evaluation |
| Change detector | Compare current and prior versions | Clause alignment, diff provenance |
| HANA repository | Store regulatory and business data | Tenant isolation, access control |
| Retrieval service | Retrieve clauses relevant to a question/check | Source filters, metadata filters |
| Rules engine | Evaluate explicit applicability conditions | Deterministic tests and rule version |
| Agent orchestrator | Coordinate bounded analysis tasks | Tool allowlist, schema validation |
| API layer | Expose application operations | Authentication, authorization, validation |
| Web client | Present findings and review workflows | No secrets or privileged logic in browser |
| MLOps | Track datasets, models, evaluation and releases | Approval gates and rollback |

## 4. Data flow sequence

```mermaid
sequenceDiagram
    participant P as Official Portal
    participant I as Ingestion
    participant X as Extraction + DL
    participant H as SAP HANA Cloud
    participant A as Agent Workflow
    participant R as Rules Engine
    participant U as React UI
    participant V as Reviewer

    P->>I: Publish notification
    I->>I: Fetch and hash document
    I->>X: Submit new document
    X->>X: Extract pages, clauses and fields
    X->>H: Store document and candidate clauses
    H->>H: Compare with prior version metadata
    H->>A: Provide candidate regulatory change
    A->>H: Retrieve relevant clauses and business profile
    A->>R: Request deterministic applicability checks
    R-->>A: Return matched records and rule results
    A->>H: Store evidence-backed candidate finding
    A->>U: Return finding and proposed action
    U->>V: Present evidence for review
    V->>U: Approve / reject / amend
    U->>H: Persist review decision and audit record
```

## 5. Agent workflow

```mermaid
flowchart TD
    START["Candidate regulatory change"] --> REG["Regulation Agent"]
    REG --> CHECK{"Evidence complete and fields valid?"}
    CHECK -- No --> HUMAN["Escalate for manual review"]
    CHECK -- Yes --> IMP["Impact Agent"]
    IMP --> RULE["Deterministic applicability checks"]
    RULE --> RESULTS["Matched business records"]
    RESULTS --> REM["Remediation Agent"]
    REM --> REVIEW["Human review queue"]
    REVIEW --> DEC{"Reviewer decision"}
    DEC -- Approve --> PUBLISH["Publish reviewed finding"]
    DEC -- Reject / amend --> FEEDBACK["Store reviewer correction"]
    PUBLISH --> END["Audit history"]
    FEEDBACK --> END
    HUMAN --> END
```

## 6. HANA logical entities

Use relational tables for transactional integrity and relationships. Consider HANA graph or vector capabilities only if available and justified.

- `ORGANISATION`
- `REGULATORY_SOURCE`
- `REGULATORY_DOCUMENT`
- `DOCUMENT_VERSION`
- `DOCUMENT_CLAUSE`
- `REGULATORY_RULE`
- `BUSINESS_PRODUCT`
- `SHIPMENT`
- `BUSINESS_DOCUMENT`
- `IMPACT_FINDING`
- `REMEDIATION_ACTION`
- `REVIEW_DECISION`
- `MODEL_RUN`

Store a stable identifier for each source and clause. Keep document versions rather than overwriting them.

## 7. Technology boundaries

- React/TypeScript is the presentation layer.
- FastAPI owns application APIs and orchestration entry points.
- SAP HANA Cloud is the primary data platform.
- LangGraph coordinates bounded agent tasks.
- PyTorch/Hugging Face supports the selected document-understanding task.
- MLflow/DVC provide experiment and dataset lineage.
- LLMs produce structured proposals grounded in retrieved clauses; deterministic code validates explicit conditions.

## 8. Deployment view

```mermaid
flowchart LR
    B["Browser"] -->|HTTPS| FE["Frontend hosting"]
    FE -->|HTTPS API| BE["FastAPI container"]
    BE --> HANA[("SAP HANA Cloud")]
    BE --> LLM["Approved LLM endpoint"]
    BE --> MODEL["DL inference service"]
    JOB["Scheduled ingestion worker"] --> BE
    MLOPS["CI/CD + MLflow + DVC"] --> MODEL
```

Deployment topology depends on the available SAP BTP subscription, HANA Cloud tenant, model endpoint and hosting permissions. Confirm these before finalizing infrastructure.
