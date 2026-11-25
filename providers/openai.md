# OpenAI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#openai)

> [Official docs: Data Controls](https://developers.openai.com/api/docs/guides/your-data)

- **Control Name**: Zero Data Retention (ZDR) / Modified Abuse Monitoring (MAM)
- **Default retention**: Prompts stored up to 30 days for abuse monitoring
- **How to enable ZDR**: Enterprise sales approval required → Dashboard: **Settings → Organization → Data Retention** → configure at org or project level
- **ZDR behavior**: The `store` parameter is always treated as `false`, even if set to `true` in requests
- **MAM alternative**: Excludes customer content from abuse monitoring logs but keeps the `store` parameter functional — for orgs that need data retention but reduced monitoring

**ZDR-Eligible Endpoints:**
`/v1/chat/completions`, `/v1/responses`, `/v1/images/*`, `/v1/embeddings`, `/v1/audio/*`, `/v1/moderations`, `/v1/completions`, `/v1/realtime`

**NOT ZDR-Eligible:**
Assistants API (`/v1/assistants`, `/v1/threads`, `/v1/vector_stores`), Conversations API, Files, Fine-tuning, Batches, Evals, Background mode (`/v1/responses` with `background: true`), Hosted containers (Code Interpreter)

**Additional Controls:**
- **Data Residency**: Available for EU (`eu.api.openai.com`), AU (`au.api.openai.com`) — requires ZDR amendment, 10% cost uplift
- **Enterprise Key Management (EKM)**: Encrypt application state using your external KMS (AWS, GCP, Azure)
- **Extended prompt caching**: Stores GPU-local tensors with 24-hour expiry — incompatible with strict ZDR

```bash
# ZDR is org/project-level, not per-request. Once enabled, store is always false:
curl https://api.openai.com/v1/chat/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-4o",
    "store": false,
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
