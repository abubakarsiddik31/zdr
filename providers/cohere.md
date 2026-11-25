# Cohere: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#cohere)

Cohere's hosted API logs prompts and generations for 30 days. ZDR is available only to approved enterprise customers. Training on your prompts is opt-out, so check the dashboard setting first. If you cannot send data to Cohere at all, its private deployments mean Cohere never receives your prompts.

## Default retention

- Logged prompts and generations are deleted automatically after 30 days, unless the law or a contract requires longer, or the usage is flagged as possibly violating Cohere's terms. [1]
- You can opt out of your prompts and generations being used to train Cohere models with the toggle under Data Controls in the dashboard settings. [1]

## Getting ZDR

- With ZDR approved, Cohere does not log any customer prompts or generations. [1]
- It is limited to enterprise customers who make additional usage commitments. [1]
- To request it, email support@cohere.com and copy your sales representative. [1]
- ZDR does not cover usage data such as billing and token counts. [1]

## Private deployment

For on-premises or isolated VPC deployments, and for Cohere models on third-party cloud ML platforms, Cohere does not receive any customer prompts or generations. [1] On a cloud platform, that platform's own retention terms apply; see [AWS Bedrock](aws-bedrock.md) or [Azure OpenAI](azure-openai.md) for how those handle data.

## Compliance

Cohere's Trust Center offers its ISO 27001 and ISO 42001 certificates and a SOC 2 Type II report on request. [1][2]

## How to check it

- In the dashboard, open Data Controls and confirm the training opt-out is set. Screenshot it.
- Get ZDR approval in writing from Cohere, naming the account or organization it applies to.
- Confirm which API keys belong to the approved account. A key from another account would fall back to 30-day logging.

## Sources

1. Cohere, [Enterprise data commitments](https://cohere.com/enterprise-data-commitments). Accessed 25 November 2026.
2. Cohere, [Security](https://cohere.com/security). Accessed 25 November 2026.

---

[← All providers](../README.md#-provider-guides) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
