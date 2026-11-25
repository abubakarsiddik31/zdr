# OpenRouter: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#openrouter)

OpenRouter is a router, so there are two policies to check: OpenRouter's own and that of the provider serving each request. OpenRouter does not log prompts or completions unless you opt in. The provider might, so turn on ZDR enforcement, which limits routing to providers with zero retention. Do not opt into prompt logging for the 1% discount if the data is sensitive. The licence you grant is broad.

## OpenRouter's own handling

- By default OpenRouter logs only request metadata, such as timestamps, the model used and token counts. It does not log prompts or completions, even on errors, unless you opt in. [1]
- Opting into prompt logging earns a 1% discount on usage. [1] It also turns on chat logging, and grants OpenRouter a perpetual, irrevocable licence to your content for its own commercial purposes, including licensing or selling it in anonymized form. [2]
- From 27 October 2026, OpenRouter screens all inference requests, ZDR traffic included, for CSAM. Flagged content can be kept for review, reporting and legal preservation. [3]

## Enforcing ZDR

Account-wide: in [privacy settings](https://openrouter.ai/settings/privacy), turn on ZDR for each model group. There are separate toggles for Anthropic, OpenAI, Google, SpaceXSI and all other models. Guardrails expose the same settings as `enforce_zdr_anthropic`, `enforce_zdr_openai`, `enforce_zdr_google`, `enforce_zdr_xai` and `enforce_zdr_other`. The older single `enforce_zdr` field is deprecated. [3]

Per request: set `provider.zdr`. It is combined with the account and guardrail settings using OR, so a request can add enforcement but cannot turn it off. [3]

```json
{
  "model": "<model>",
  "messages": [{ "role": "user", "content": "Hello" }],
  "provider": { "zdr": true }
}
```

Test a model with no ZDR endpoint before you rely on this. The request should return an error, the way OpenRouter documents for `data_collection: "deny"` [4], rather than fall back to an endpoint that retains data.

Things to know about the model groups: [3]

- The Anthropic scope removes Anthropic's own endpoints, but Bedrock and Vertex endpoints for Claude remain.
- The OpenAI scope removes OpenAI's own endpoints, but Azure remains.
- The Google scope removes AI Studio endpoints.

## ZDR, Safety Retention and data collection

| Setting | What it limits |
| :--- | :--- |
| `zdr` | Only endpoints with no routine storage of prompts or responses after processing. [3] |
| Safety Retention | A separate tier where the provider may keep prompts and responses to detect and prevent abuse, but not for training. Google Vertex models require it from 18 October 2026. You allow or exclude it for Google and other models separately. [3] |
| `data_collection: "deny"` | Only providers that do not store user data non-transiently or train on it. The default is `"allow"`. [4] |

OpenRouter counts in-memory implicit prompt caching as compatible with ZDR. [3] If it cannot establish a clear policy for an endpoint, it assumes the endpoint retains data and trains on it. [3]

The list of eligible endpoints is available from the API and updates when a provider's policy changes:

```bash
curl https://openrouter.ai/api/v1/endpoints/zdr
```

## In-region routing

On Business and Enterprise plans, requests sent to the EU hostname are processed in the EU and do not leave the region. [5]

## How to check it

- In privacy settings, confirm ZDR is on for every model group you use, and that prompt logging is off.
- Add `provider.zdr: true` in code as well, so a change in account settings can't silently widen routing.
- Check the response metadata or activity log for which provider served each request, and compare it with the ZDR endpoint list.

## Sources

1. OpenRouter, [FAQ](https://openrouter.ai/docs/faq). Accessed 25 November 2026.
2. OpenRouter, [Terms of Service](https://openrouter.ai/terms), section 6.2. Accessed 25 November 2026.
3. OpenRouter, [Zero Data Retention](https://openrouter.ai/docs/guides/features/zdr). Accessed 25 November 2026.
4. OpenRouter, [Provider routing](https://openrouter.ai/docs/guides/routing/provider-selection). Accessed 25 November 2026.
5. OpenRouter, [Provider logging](https://openrouter.ai/docs/guides/privacy/provider-logging). Accessed 25 November 2026.

---

[← All providers](../README.md#provider-guides) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
