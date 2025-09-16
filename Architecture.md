# 🧩 Synthetic Data as a Service (MVP) — Full Architecture

This document provides a **complete architecture overview** for the SDaaS MVP with a **Vue 3 + Vite frontend** and a **FastAPI backend**. It includes purpose, components, data flow, API contracts, repository structure, and scalability considerations. All diagrams use **Mermaid** (rendered natively in GitHub).

---

## 0) At a Glance

- **Frontend:** Vue 3 (Vite, Axios) — job wizard, status table, preview, metrics, download
- **Backend:** FastAPI (Uvicorn/ASGI) — generation jobs, metrics, storage
- **Generation:** Probabilistic samplers + lightweight tabular model (CTGAN-style placeholder)
- **Persistence:** Local filesystem (`/data`) for artifacts + sidecar JSON metadata
- **Queue:** In-memory job manager/worker loop (MVP)
- **APIs:** `POST /generate`, `GET /jobs/{id}`, `/preview/{id}`, `/metrics/{id}`, `/download/{id}`
- **Security (MVP):** CORS allowlist, simple API key (optional), rate limits (optional)

---

## 1) High-Level Architecture

```mermaid
flowchart TD
    subgraph Client
        A[Vue Frontend<br/>(Vite, Axios, SPA)]
    end

    subgraph Server
        B[FastAPI API<br/>(Uvicorn/ASGI)]
        C[Job Manager<br/>(In-Memory Queue + Worker)]
        D[Schema & Template Loader]
        E[Generator<br/>(Samplers + Tabular Model)]
        F[Evaluator<br/>(KS, Freq Dist, Uniqueness)]
        G[Storage<br/>/data/*.csv, *.json, *.meta.json]
    end

    A <--> B
    B --> C
    C --> D
    C --> E
    C --> F
    E --> G
    F --> G
    G -->|preview/metrics/download| B --> A


sequenceDiagram
    actor U as User (Vue)
    participant FE as Vue App
    participant API as FastAPI
    participant Q as JobManager
    participant GEN as Generator
    participant MET as Evaluator
    participant ST as Storage

    U->>FE: Configure template/schema, rows, format, seed
    FE->>API: POST /generate { template | schema, rows, format, seed }
    API->>Q: Create Job(id, status=queued)
    API-->>FE: { job_id, status=queued }

    loop Worker Loop
        Q->>GEN: run(job)
        GEN->>ST: write dataset (.csv|.json)
        GEN->>MET: compute metrics
        MET->>ST: write metrics (.meta.json)
        Q-->>API: update status=done|error
    end

    FE->>API: GET /jobs/{id} (poll)
    API-->>FE: { status }
    FE->>API: GET /preview/{id}?n=20
    API-->>FE: { columns, rows }
    FE->>API: GET /metrics/{id}
    API-->>FE: { utility, categoricals, privacy_lite }
    FE->>API: GET /download/{id}
    API-->>FE: stream file



classDiagram
    class VueFrontend {
        +JobWizard()
        +SchemaEditor()
        +JobsTable()
        +PreviewView()
        +MetricsView()
        +AxiosClient(baseURL)
    }

    class FastAPI {
        +POST /generate()
        +GET /jobs/{id}()
        +GET /preview/{id}()
        +GET /metrics/{id}()
        +GET /download/{id}()
    }

    class JobManager {
        +enqueue(job)
        +run_worker()
        +update_status(id, status)
        -queue
        -jobsMap
    }

    class TemplateService {
        +load(name): Schema
        -templatesDir
    }

    class SchemaService {
        +validate(input): Schema
        +normalize(schema): Schema
    }

    class GenerationService {
        +generate(schema, rows, seed): DataFrame
        -samplers
        -tabularModel
    }

    class MetricsService {
        +utility(df): Map
        +categoricals(df): Map
        +privacyLite(df): Map
    }

    class StorageService {
        +write(jobId, df, format): Path
        +writeMeta(jobId, meta): Path
        +readPreview(jobId, n): Sample
        +stream(jobId): Stream
    }

    VueFrontend --> FastAPI
    FastAPI --> JobManager
    FastAPI ..> TemplateService
    FastAPI ..> SchemaService
    JobManager ..> GenerationService
    JobManager ..> MetricsService
    GenerationService ..> StorageService
    MetricsService ..> StorageService


flowchart LR
    subgraph Dev/Local
        V[Vue Dev Server<br/>:5173]
        U[Uvicorn FastAPI<br/>:8000]
        D[(Local Disk<br/>'/data')]
    end
    V <--> U
    U <--> D

    %% Production suggestion (optional future)
    subgraph Optional Prod
        CDN[Static Hosting/CDN]
        API[FastAPI on App Server]
        OBJ[(Object Storage)]
    end
    CDN -.serves static.-> V
    API -.scales via Gunicorn/Workers.-> U
    API -.S3/GCS/Azure Blob.-> OBJ
