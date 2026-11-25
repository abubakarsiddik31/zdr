# Azure OpenAI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#azure-openai)

Azure OpenAI, now documented as "Foundry Models sold by Azure" in Microsoft Foundry, runs OpenAI models inside Microsoft's own environment. Your data does not go to OpenAI and is not used for training. The setting that matters is abuse monitoring: by default Microsoft can store flagged prompts and completions for human review, and Modified Abuse Monitoring turns that off. In November 2026 Microsoft announced that Modified Abuse Monitoring will not be available for models released on or after 1 November 2026.

## Default handling

- Prompts, completions, embeddings and training data are not available to other customers or to OpenAI, and are not used to train foundation models without your permission. The models are stateless. [1]
- Prompts and completions are checked in real time by content filters (now called Guardrails). Nothing is stored in the classifier models. [1]
- When abuse monitoring flags content or a pattern of use, a sample of prompts and completions can be reviewed. Review is automated by default (AI models, nothing stored). Where automated review isn't confident enough, authorized Microsoft employees can review the stored data from secure workstations with just-in-time approval. For resources in the EEA, those reviewers are in the EEA. [1][2]
- Flagged data for human review is stored in the same Azure geography as your resource, separated by resource. [1]

The current Microsoft pages do not state a fixed retention period for the abuse monitoring data store.

## Modified Abuse Monitoring

With Modified Abuse Monitoring approved, Microsoft does not store prompts and completions for human review. Automated review can still run at the moment of the request, and if it detects severe or recurring abuse Microsoft can limit your access or ask you to turn human review back on. [1]

Who can get it: customers and partners managed by a Microsoft account team or under an eligible program. You apply through Microsoft's [modified abuse monitoring form](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUOE9MUTFMUlpBNk5IQlZWWkcyUEpWWEhGOCQlQCN0PWcu). Some advanced models have stricter criteria. [3]

The change announced on 17 November 2026: [1][2][3]

- Models released before 1 November 2026 keep Modified Abuse Monitoring for customers who are already approved, with no change.
- For models released on or after 1 November 2026, Modified Abuse Monitoring will stop being available from a date not yet announced. Those models will then follow Microsoft's "updated approach to data retention, access, and safety monitoring", including for customers who are already approved.
- Microsoft says it will give at least 30 days' notice per model.

If your compliance case depends on Modified Abuse Monitoring, pin it to models released before 1 November 2026 for now, and watch the change log on the data privacy page [1].

## Features that store data

Some features keep data in your Foundry resource, in the same geography, encrypted at rest with Microsoft-managed keys or your own key. You can delete it at any time. [1]

- Responses API (message history)
- Assistants API threads (preview)
- Stored completions (preview)
- Files API and vector stores
- Batch processing
- Fine-tuning data and fine-tuned models

Modified Abuse Monitoring does not stop these features storing what they need. Avoid them, or delete the data, if you need nothing kept at rest.

## Where data is processed

- Standard deployments process prompts and responses in the geography you chose. [1]
- Global deployments can process in any geography where the model is deployed. [1]
- Data Zone deployments process within the data zone, for example anywhere in the United States, or anywhere in the EU for an EU resource. [1]
- For all deployment types, data at rest, including the abuse monitoring store, stays in your resource's geography. Batch is a Global deployment type. [1]

## Private networking

Put a private endpoint in front of the resource and turn off public network access.

```bash
az network private-endpoint create \
  --name openai-pe \
  --resource-group <rg> \
  --vnet-name <vnet> \
  --subnet <subnet> \
  --private-connection-resource-id <resource-id> \
  --group-id account \
  --connection-name openai-conn

az cognitiveservices account update \
  --name <resource-name> \
  --resource-group <rg> \
  --public-network-access Disabled
```

## How to check it

Once approved, the resource shows a `ContentLogging` capability set to `false`. The attribute only appears when data storage for abuse monitoring is off. [1]

```bash
az cognitiveservices account show --name <resource-name> --resource-group <rg> \
  --query "properties.capabilities[?name=='ContentLogging']"
```

In the portal, open the resource's Overview page and select JSON view.

Also record the release date of every model you deploy, given the Modified Abuse Monitoring change.

## Sources

1. Microsoft Learn, [Data, privacy, and security for Foundry Models sold by Azure](https://learn.microsoft.com/en-us/azure/foundry/responsible-ai/openai/data-privacy). Change log entry 17 November 2026. Accessed 25 November 2026.
2. Microsoft Learn, [Foundry Models sold by Azure abuse monitoring](https://learn.microsoft.com/en-us/azure/foundry/openai/concepts/abuse-monitoring). Accessed 25 November 2026.
3. Microsoft Learn, [Limited access to Azure OpenAI in Microsoft Foundry Models](https://learn.microsoft.com/en-us/azure/foundry/responsible-ai/openai/limited-access). Accessed 25 November 2026.

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
