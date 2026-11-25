# Mistral AI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#mistral-ai)

> [Official docs: ZDR](https://help.mistral.ai/en/articles/347612-can-i-activate-zero-data-retention-zdr) · [Data Governance](https://help.mistral.ai/en/collections/789667-data-governance)

- **Default retention**: API inputs/outputs retained for 30 rolling days for abuse monitoring
- **How to enable ZDR**: Activate ZDR on your account — 30-day abuse window no longer applies
- **Training**: API data is **never** used for training — contractual guarantee
- **Self-hosting**: Open-weight models (Mistral 7B, Mixtral) available under Apache 2.0. Mistral Large 3 (675B MoE, 41B active) can be self-hosted on 8xH100

**Current models (April 2026):**
- Mistral Large 3 — 675B total / 41B active (MoE), 256K context
- Mistral Medium 3 — balanced workloads, deployable on 4+ GPUs
- Mistral Small 4 — high-throughput, low-latency

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
