# Azure OpenAI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#azure-openai)

> [Official docs: Data Privacy](https://learn.microsoft.com/en-us/legal/cognitive-services/openai/data-privacy) · [Abuse Monitoring](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/abuse-monitoring)

- **Default**: Prompts/completions are **not** used for model training. Abuse monitoring retains data up to 30 days
- **How to enable ZDR**: Apply for **Modified Abuse Monitoring** exception via Azure support ticket. Requires Enterprise Agreement (EA) or Microsoft Customer Agreement (MCA) — not available on Pay-As-You-Go
- **Verification**: Check resource capabilities for `ContentLogging: false`
- **Scope**: All Azure OpenAI models (GPT-4o, GPT-4.1, o-series, DALL-E, Whisper, embeddings)

**Private Networking:**
```bash
# Create Private Endpoint — traffic stays off public internet
az network private-endpoint create \
  --name openai-pe \
  --resource-group <rg> \
  --vnet-name <vnet> \
  --subnet <subnet> \
  --private-connection-resource-id <openai-resource-id> \
  --group-id account \
  --connection-name openai-conn

# Disable public access
az cognitiveservices account update \
  --name <resource-name> \
  --resource-group <rg> \
  --public-network-access Disabled
```

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
