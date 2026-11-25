# Hugging Face Inference Endpoints: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#hugging-face-inference-endpoints)

> [Official docs: Security & Compliance](https://huggingface.co/docs/inference-endpoints/en/security)

- **Payload storage**: None — Hugging Face does not store customer payloads or tokens
- **Logs**: Stored for 30 days
- **Endpoint types**:
  - **Public**: TLS/SSL, no auth required
  - **Protected**: TLS/SSL + HF token required
  - **Private**: Only via intra-region AWS or Azure PrivateLink — not accessible from internet
- **Compliance**: SOC 2 Type 2, GDPR DPA available via Enterprise Hub
- **Infrastructure**: Deploy any model on dedicated CPUs, GPUs, TPUs, or AWS Inferentia 2. Autoscaling + scale-to-zero

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
