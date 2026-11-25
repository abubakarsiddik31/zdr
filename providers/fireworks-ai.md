# Fireworks AI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#fireworks-ai)

> [Official docs: Zero Data Retention](https://docs.fireworks.ai/guides/security_compliance/data_handling) · **Full setup guide:** [Fireworks AI ZDR: how to set it up without missing the exceptions](https://abubakar-siddik.com/guides/fireworks-ai-zero-data-retention)

- **Default**: **ZDR by default** — no prompt or completion data is logged or stored. Data exists only in volatile memory for the duration of the request
- **Prompt caching**: If active, some data stored in volatile memory for several minutes — never persisted to disk
- **Logging opt-in**: You can explicitly opt in to logging for features like FireOptimizer
- **Compliance**: SOC 2 Type II + HIPAA compliant. TLS 1.2+ in transit, AES-256 at rest
- **Training**: Data never used to train or improve models without explicit opt-in

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
