# OpenRouter: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#openrouter)

> [Official docs: ZDR](https://openrouter.ai/docs/guides/features/zdr) · [Provider Routing](https://openrouter.ai/docs/guides/routing/provider-selection)

OpenRouter **does not log prompts by default**. It stores only request metadata (timestamps, model, token counts, latency) for billing.

**How to enforce ZDR routing:**
1. **Account-wide**: Settings → Privacy → "Only allow Zero Data Retention providers"
2. **Per-request**: Pass `provider.data_collection: "deny"` — if the chosen model's provider doesn't support ZDR, the request fails cleanly

```json
{
  "model": "anthropic/claude-sonnet-4",
  "messages": [{"role": "user", "content": "Hello"}],
  "provider": {
    "data_collection": "deny"
  }
}
```

**Caveats:**
- **Prompt Logging Discount**: 1% cost discount if you opt in to prompt logging — **this gives OpenRouter the right to use your data commercially**. Ensure it's disabled if privacy matters
- **Implicit caching**: OpenRouter considers in-memory caching (not persisted) as compatible with ZDR
- ZDR providers via OpenRouter include: Google (Vertex), Amazon (Bedrock), DeepInfra, NovitaAI, and others

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
