# AI Gateways with Zero Data Retention

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#other-gateways)

Enterprise gateways enforce ZDR policies across multiple upstream providers through a unified interface.

For OpenRouter, see [openrouter.md](openrouter.md).

| Gateway | ZDR Feature | Use Case |
| :--- | :--- | :--- |
| **Cloudflare AI Gateway** | [Zero Data Retention toggle](https://developers.cloudflare.com/ai-gateway/observability/logging/) | Edge observability + privacy for multiple providers |
| **Portkey.ai** | Log redaction, vault, guardrails | Enterprise orchestration + compliance |
| **LiteLLM** | Presidio PII masking integration | Open-source proxy with DLP middleware |

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
