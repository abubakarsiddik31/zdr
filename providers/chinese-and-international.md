# Chinese & International Providers: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#chinese--international-providers)

Major Chinese providers typically achieve enterprise privacy via **Private Cloud**, **VPC Deployments**, or **Self-Hosting** rather than a ZDR API toggle.

| Provider | Model | Privacy Strategy | ZDR Readiness |
| :--- | :--- | :--- | :--- |
| **DeepSeek** | DeepSeek-R1 / V3 | **Self-Hosting (MIT License)** | Full (on your infra via vLLM/SGLang) |
| **Zhipu AI** | GLM-4 series | Private VPC Deployment | Enterprise Only (dedicated clusters) |
| **Alibaba** | Qwen 3.5 / Qwen3 series | Alibaba Cloud PAI-EAS, or self-host (Apache 2.0) | High (self-host or dedicated isolation) |
| **Moonshot** | Kimi | Route via gateways (e.g., OpenRouter) | Limited (router enforces ZDR) |

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
