# Together AI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#together-ai)

New Together AI organizations start with zero data retention. Older organizations may still have prompt storage on, and that matters: in November 2026 Together disclosed that stored prompt samples from June and July 2026 were downloaded by an unauthorized party. If your organization predates the change, check the setting today.

## Default handling

- Organizations created after the change start with prompt storage off, which means ZDR. [1][2]
- Organizations with prompt storage on have their prompts and responses stored, and Together may use them for product improvements. [2]
- Training on your data is a separate opt-in and is off by default. [1]

## Turning on ZDR

In Organization Settings, open the Privacy section and turn off Prompt storage. [1]

- ZDR only applies to requests sent after you turn it on. To delete older data, use the delete option in settings or contact Together's privacy team. [1]
- Turning off prompt storage also disables passthrough models, because they need retained prompts. Passthrough models send your request to the model's original provider under that provider's policy. Models Together hosts itself, such as DeepSeek or Qwen, run on Together's own infrastructure. [1]

## What ZDR still keeps

From Together's ZDR page: [1]

- Request content can sit in memory and temporary caches while the request runs, but is not written to persistent storage.
- Request metadata (token counts, model IDs, timestamps) for billing and the usage dashboard.
- Account, organization and billing data.
- Files you upload for fine-tuning or the Batch API, until you delete them.

## The 2026 incident

Together published the details on 21 November 2026. [3]

- Together had collected prompt samples for software testing and kept them in a private Hugging Face repository.
- An AI coding agent committed a credential for that repository to a public GitHub repository. The commit was reverted, but an unauthorized party had already obtained the credential and downloaded the samples.
- The samples came mainly from requests on 24 June and 4, 8 and 14 July 2026, to MiniMax M3, GLM-5.2 and Kimi K2.6. The party searched them for credentials to other services.
- Together's fixes include scoped and short-lived credentials, checking every pull request for secrets, new rules for AI coding agents, and making ZDR the default for new accounts. Its advice is to turn on ZDR if you send sensitive prompts.

The post does not say whether the affected requests came from organizations with ZDR on. It is a good example of why "we don't train on it" and "we don't store it" are different promises.

## Data residency and private deployment

- Requests to `us-api.together.ai` keep inference processing in the United States for selected serverless models. Together says this covers only where requests are processed, not where data is stored, logged or routed. [2]
- For GDPR or stricter residency, Together offers dedicated endpoints with private networking and VPC deployments, including EU regions, through sales. [1][2]

## How to check it

- In Organization Settings, confirm Prompt storage is off. Screenshot it.
- If the organization is older than the ZDR default (Together does not give the date of the change), check whether storage was ever on, and delete any stored data.
- List any fine-tuning or Batch files and delete the ones you no longer need.
- Confirm no code depends on passthrough models.

## Sources

1. Together AI, [Zero data retention](https://docs.together.ai/docs/zero-data-retention). Accessed 25 November 2026.
2. Together AI, [Privacy and security](https://docs.together.ai/docs/privacy-and-security). Accessed 25 November 2026.
3. Together AI, [Security event: prompt leakage for requests in June and July of 2026](https://www.together.ai/blog/security-event-prompt-leakage-for-requests-in-june-and-july-of-2026). 21 November 2026. Accessed 25 November 2026.

---

[← All providers](../README.md#provider-guides) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
