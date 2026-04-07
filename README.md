# 🛡️ Zero-Retention Database for LLM Providers

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://GitHub.com/zdr-database/zdr/graphs/commit-activity)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

A comprehensive, industry-standard guide to **Zero-Retention (ZDR)** endpoints across major Enterprise LLM providers. This repository provides technical controls, contract requirements, and configuration steps for regulated industries (Finance, Healthcare, Legal) requiring strict data privacy.

---

## 📑 Table of Contents
- [Executive Summary](#executive-summary)
- [Terminology & Evaluation Criteria](#terminology--evaluation-criteria)
- [Tier 1: Major US Cloud Providers](#tier-1-major-us-cloud-providers)
  - [OpenAI](#openai)
  - [Anthropic](#anthropic)
  - [Google Vertex AI](#google-vertex-ai)
  - [Microsoft Azure OpenAI](#microsoft-azure-openai)
  - [Amazon Bedrock](#amazon-bedrock)
- [Tier 2: Reasoning & Deep Research Models](#tier-2-reasoning--deep-research-models)
- [Tier 3: Chinese & International Providers](#tier-3-chinese--international-providers)
- [Gateways & Enterprise Routers](#gateways--enterprise-routers)
- [Global Comparison Table](#global-comparison-table)
- [Verification & Audit Guide](#verification--audit-guide)
- [Architecture Blueprints](#architecture-blueprints)
- [Contributing](#contributing)

---

## 🚀 Executive Summary

"Zero-retention" is not a single feature; it is a **bundle of technical controls + contract terms** ensuring customer content (prompts, outputs, files) is not stored at rest by the vendor.

### Key Patterns
- **Hyperscalers (AWS, Azure, OCI)**: Position base inference as "no storage by default," with optional logging.
- **Model Native APIs (OpenAI, Anthropic)**: Provide ZDR as an **approved enterprise setting** governed by contract.
- **Sovereign/Chinese Models**: Primarily rely on **Private Cloud/VPC** or **Self-Hosting** to guarantee non-retention.

---

## 🔍 Terminology & Evaluation Criteria

- **Customer Content**: Prompts, outputs, and uploaded files.
- **Abuse Monitoring**: Safety logs used to detect misuse (often the primary "retention" loophole).
- **Application State**: Stateful features (Threads, Vector Stores) that require TTL-based storage.
- **System Data**: Billing metadata, token counts (usually excluded from ZDR).

---

## 🏢 Tier 1: Major US Cloud Providers

### OpenAI
- **Control Name**: Zero Data Retention (ZDR) / Modified Abuse Monitoring (MAM).
- **Setup**: Approval required → Dashboard: **Settings → Organization → Data controls**.
- **Caveat**: Prompt Caching and Assistants API are NOT ZDR-eligible.

### Anthropic
- **Control Name**: ZDR Arrangement / HIPAA-Ready Organization.
- **Setup**: Contract addendum required. Use the Messages API with `inference_geo` control.
- **Caveat**: Flagged misuse may be retained for up to 2 years under safety exceptions.

### Google Vertex AI
- **Control Name**: Vertex AI Zero Data Retention Posture.
- **Setup**: Request **Abuse Monitoring Exception**. Disable BigQuery request/response logging.
- **Caveat**: Grounding with Google Search/Maps breaks strict ZDR (30-day storage for logs).

---

## 🧠 Tier 2: Reasoning & Deep Research Models

| Provider | Model | Native ZDR API? | Reasoning Tokens Covered? | Verification Method |
| :--- | :--- | :--- | :--- | :--- |
| **OpenAI** | o1, o3-mini | **Yes** | Yes (via encrypted items) | Pass `reasoning.encrypted_content` |
| **Anthropic** | Claude Thinking | **Yes** | Yes (ZDR-eligible) | In-memory processing only |
| **Google** | Gemini Thinking | **Yes** | Yes | Follows Vertex AI Exception |

---

## 🌏 Tier 3: Chinese & International Providers

Chinese providers typically do not offer a "one-click" ZDR toggle for public APIs. Privacy is achieved via **Private Cloud** or **Self-Hosting**.

| Provider | Model | Privacy Strategy | ZDR Readiness |
| :--- | :--- | :--- | :--- |
| **DeepSeek** | R1 | **Self-Hosting (MIT)** | Full (on your infra) |
| **Zhipu AI** | GLM-4/5 | Private VPC Deployment | Enterprise Only |
| **Moonshot** | Kimi | Route via OpenRouter ZDR | Limited (Router only) |
| **Alibaba** | Qwen 2.5 | Alibaba Cloud PAI-EAS | High (Dedicated isolation) |

---

## 🗺️ Global Comparison Table

| Provider | Mechanism | How to Enable | Private Networking |
| :--- | :--- | :--- | :--- |
| **OpenAI** | ZDR/MAM | Sales Approval | Public SaaS Only |
| **Azure OpenAI** | Opt-out | ContentLogging: false | Azure Private Endpoint |
| **AWS Bedrock** | Default | No Logging Opt-in | AWS PrivateLink |
| **DeepSeek** | Open Source | Deploy via vLLM | Full VPC Isolation |

---

## 🛡️ Verification & Audit Guide

A credible ZDR audit requires **Four Pillars of Evidence**:
1. **Configuration Artifacts**: CLI output showing `ContentLogging: false`.
2. **Negative Tests**: Attempt to retrieve a completion by ID; confirm `404 Not Found`.
3. **Environment Logs**: Ensure your own WAF/Gateway isn't logging payloads.
4. **Contractual Proof**: Signed DPA or ZDR Addendum.

---

## 📐 Architecture Blueprints

### VPC-Native Isolated Pattern
```mermaid
flowchart LR
  subgraph Customer_VPC["Customer VPC/VNet"]
    App["App"]
    Proxy["DLP / Redaction Proxy"]
  end
  subgraph Private_Link["PrivateLink"]
    PE["Private Endpoint"]
  end
  subgraph Provider["LLM Provider"]
    LLM["Model (Stateless)"]
  end
  App --> Proxy --> PE --> LLM
```

---

## 🤝 Contributing
We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to add new providers or update existing ones.

---

## ⚖️ License
Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.
