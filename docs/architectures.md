# Architecture Blueprints for Private LLM Use

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#architecture-blueprints)

## 1. Cloud ZDR with Private Networking

The enterprise standard: frontier models via private network, no data on public internet.

```mermaid
flowchart TB
    subgraph CustomerVPC["Customer VPC / VNet"]
        direction TB
        App["Application Server"]
        DLP["DLP Proxy\n(Presidio · Bedrock Guardrails)"]
        Logs["Audit Logs\n(metadata only)"]
        WAF["WAF / Rate Limiter"]
    end

    subgraph PrivateLink["Private Connectivity"]
        PE["AWS PrivateLink\nAzure Private Endpoint\nGCP Private Service Connect"]
    end

    subgraph Provider["LLM Provider"]
        direction TB
        LB["Load Balancer"]
        GPU1["Model Instance A"]
        GPU2["Model Instance B"]
        LB --> GPU1
        LB --> GPU2
    end

    App --> DLP
    DLP --> WAF
    WAF -.->|"metadata only"| Logs
    WAF --> PE
    PE --> LB

    style CustomerVPC fill:#eef6ff,stroke:#4a90d9
    style PrivateLink fill:#fff8e1,stroke:#f39c12
    style Provider fill:#e8f5e9,stroke:#2ecc71
    style DLP fill:#2ecc71,stroke:#1a9c54,color:#fff
    style Logs fill:#3498db,stroke:#2471a3,color:#fff
```

## 2. Self-Hosted Production Stack

Maximum privacy: everything runs on your infrastructure, nothing leaves.

```mermaid
flowchart TB
    subgraph Internet["Public Internet"]
        Users["Users / Client Apps"]
    end

    subgraph DMZ["DMZ"]
        TLS["TLS Termination\n(NGINX / Caddy)"]
        Auth["Auth Proxy\n(OAuth2 / API Key)"]
    end

    subgraph PrivateNet["Private Network (No Egress)"]
        DLP["PII Redaction\n(Presidio)"]
        LB["Load Balancer"]
        subgraph GPUCluster["GPU Cluster"]
            V1["vLLM Instance 1\n(Llama 4 Scout)"]
            V2["vLLM Instance 2\n(DeepSeek-R1-32B)"]
        end
        Metrics["Prometheus + Grafana\n(token counts, latency)"]
    end

    subgraph Storage["Encrypted Storage"]
        Weights["Model Weights\n(checksummed)"]
        AuditLog["Audit Log\n(who/when/model, no prompts)"]
    end

    Users --> TLS
    TLS --> Auth
    Auth --> DLP
    DLP --> LB
    LB --> V1
    LB --> V2
    V1 -.-> Metrics
    V2 -.-> Metrics
    V1 -.- Weights
    V2 -.- Weights
    Auth -.->|metadata| AuditLog

    style Internet fill:#fce4ec,stroke:#e74c3c
    style DMZ fill:#fff3e0,stroke:#f39c12
    style PrivateNet fill:#e8f5e9,stroke:#2ecc71
    style GPUCluster fill:#e3f2fd,stroke:#3498db
    style Storage fill:#f3e5f5,stroke:#9b59b6
```

## 3. Gateway-Based Multi-Provider ZDR

Route to the best model while enforcing ZDR across all providers.

```mermaid
flowchart LR
    subgraph App["Your Application"]
        Code["App Code"]
        SDK["OpenAI-compatible SDK"]
    end

    subgraph Gateway["AI Gateway"]
        Router["Router\n(ZDR filter ON)"]
        Cache["Response Cache\n(optional, in-memory)"]
        Fallback["Fallback Logic"]
    end

    subgraph ZDR_Providers["ZDR Providers"]
        direction TB
        A["Anthropic\n(Claude)"]
        B["AWS Bedrock\n(Llama · Titan)"]
        C["Google Vertex\n(Gemini)"]
        D["Fireworks\n(open-weight)"]
    end

    subgraph Blocked["Non-ZDR Providers"]
        X1["Provider X\n(logs prompts)"]
        X2["Provider Y\n(trains on data)"]
    end

    Code --> SDK --> Router
    Router --> Cache
    Router --> A
    Router --> B
    Router --> C
    Router --> D
    Router -.->|"blocked"| Fallback
    Fallback -.->|"❌ rejected"| X1
    Fallback -.->|"❌ rejected"| X2

    style App fill:#eef6ff,stroke:#4a90d9
    style Gateway fill:#fff8e1,stroke:#f39c12
    style ZDR_Providers fill:#e8f5e9,stroke:#2ecc71
    style Blocked fill:#fce4ec,stroke:#e74c3c
    style X1 fill:#e74c3c,stroke:#c0392b,color:#fff
    style X2 fill:#e74c3c,stroke:#c0392b,color:#fff
```

## 4. Compliance-Ready Healthcare Architecture (HIPAA)

```mermaid
flowchart TB
    subgraph CDE["HIPAA-Compliant Environment"]
        direction TB
        EHR["EHR System\n(Epic · Cerner)"]
        PHI_Strip["PHI Stripping Layer\n(Presidio · Comprehend)"]
        AppServer["Application Server"]
        AuditDB[("Audit Trail DB\n(encrypted)")]
    end

    subgraph Cloud["Cloud Provider (BAA Signed)"]
        subgraph VPC_Private["Private Subnet"]
            PE2["PrivateLink Endpoint"]
            Bedrock["AWS Bedrock\n(ZDR default)"]
        end
    end

    EHR -->|"Patient record\n(contains PHI)"| PHI_Strip
    PHI_Strip -->|"De-identified text\n(PHI removed)"| AppServer
    AppServer --> PE2
    PE2 --> Bedrock
    Bedrock --> PE2
    PE2 --> AppServer
    AppServer -->|"Re-identified response"| EHR
    AppServer -.->|"access log"| AuditDB

    style CDE fill:#e8f5e9,stroke:#27ae60
    style Cloud fill:#eef6ff,stroke:#4a90d9
    style VPC_Private fill:#e3f2fd,stroke:#3498db
    style PHI_Strip fill:#2ecc71,stroke:#1a9c54,color:#fff
    style AuditDB fill:#9b59b6,stroke:#7d3c98,color:#fff
    style EHR fill:#f39c12,stroke:#d68910,color:#fff
```

---

[← Back to the guide](../README.md) · Spotted a change in this page? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
