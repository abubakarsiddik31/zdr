# Replicate: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#replicate)

> [Official docs: Data Retention](https://replicate.com/docs/topics/predictions/data-retention)

- **API predictions**: Inputs, outputs, files, and logs **auto-deleted after 1 hour**. Save your own copies before deletion
- **Web predictions**: Kept indefinitely unless manually deleted
- **No explicit ZDR toggle** — the 1-hour auto-deletion is the default behavior
- **Training**: No blanket no-training guarantee in privacy policy. Contact privacy@replicate.com for enterprise terms
- **Webhooks**: Use webhooks to capture prediction data before the 1-hour window expires

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
