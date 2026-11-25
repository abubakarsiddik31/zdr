# Anthropic: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#anthropic)

Anthropic does not train on API data without your permission and deletes API inputs and outputs within 30 days by default. It now has three arrangements for stricter handling: Legacy ZDR, HIPAA readiness, and Enterprise Frontier Safeguards (EFS) for the models that otherwise require 30-day retention. Each covers a different set of features, so the feature table below matters more than the contract name.

## Default retention

- Inputs and outputs on the commercial API are deleted from Anthropic's back end within 30 days of receipt or generation. [1]
- Retained data is never used for model training without your express permission. [2]
- If automated trust and safety systems flag a chat as a Usage Policy violation, inputs and outputs can be kept for up to 2 years and classifier scores for up to 7 years. This applies under every arrangement, including ZDR. [1][2]
- Stateful features keep their own data: Files API uploads until you delete them, Batch API results for 29 days, code execution containers for up to 30 days. [2]

## Arrangements

| Arrangement | What it means | How to get it |
| :--- | :--- | :--- |
| Legacy ZDR | Prompts and responses are not stored at rest after the API returns the response, for eligible features. | Through Anthropic sales, enabled per organization. [2] |
| HIPAA readiness | A signed BAA plus safeguards (encryption, access controls, audit logging). Data can be retained under those safeguards. You do not also need ZDR. The API returns a `400` if a request uses a non-eligible feature. | Self-serve in the Console with the standard BAA, or through sales for a negotiated BAA. Permanent once enabled. [2] |
| Enterprise Frontier Safeguards (EFS ZDR) | ZDR for Anthropic's most capable models. Encrypted "Sealed Safety Data" goes to a bucket in your own cloud account, and your team, not Anthropic, reviews flagged Safety Cases. In beta since November 2026; no extra charge from Anthropic. | Account team or the EFS request form, then accept the terms in the Console. Applies to a whole workspace. [3] |

## Models that need retention

Anthropic designates some models as Covered Models that require 30-day retention: the Claude Fable 5, 5.1 and 5.2 and Claude Mythos 5, 5.1 and 5.2 lines. They are not available under Legacy ZDR unless Anthropic expressly authorizes it, and a request from an organization without retention returns a `400 invalid_request_error`. Organizations with Legacy ZDR can turn on 30-day retention for a single workspace to use them, or move that workspace to EFS. [2]

## Feature eligibility

A shortened version of Anthropic's table. Check the full table [2] before you rely on a feature, since it changes with new releases.

| Feature | Legacy ZDR | HIPAA | Notes |
| :--- | :--- | :--- | :--- |
| Messages API, token counting | Yes | Yes | |
| Prompt caching | Yes | Yes | KV cache and hashes held in memory for the cache TTL. |
| Structured outputs, strict tool use | Yes (qualified) | Yes | The JSON schema is cached for up to 24 hours. Keep PHI out of schemas. |
| Thinking, citations, PDF support (inline) | Yes | Yes | |
| Web search | Yes | Yes | Dynamic filtering versions are not eligible. |
| Web fetch | Yes | No | Dynamic filtering versions are not eligible. |
| Client-side tools (bash, text editor, memory, computer use) | Yes | Yes | They run in your environment. |
| Data residency (`inference_geo`) | Yes | Yes | |
| Batch API | No | No | 29-day retention. |
| Files API | No | No | Kept until deleted or expired. |
| Code execution, programmatic tool calling | No | No | Container data up to 30 days. |
| MCP connector, Agent Skills | No | No | Standard retention. |
| Message threads | No | No | Held in memory, deleted within 1 hour of last use. |
| Claude Managed Agents | No | No | Except self-hosted Managed Agents under Legacy ZDR. |

Under Legacy ZDR the API does not block "No" features. Using one moves that data outside your arrangement. [2]

## What ZDR does not cover

From Anthropic's docs: [2][4]

- The Claude Console and its playground.
- Consumer plans (Free, Pro, Max, Ultra), including Claude Code on those plans.
- The Claude Team and Enterprise web and desktop interfaces. Claude Code on Claude Enterprise has its own, separate ZDR offering.
- Claude for Excel.
- Third-party tools and integrations you connect.
- Claude Code usage statistics when metrics logging is on.

Organizations with ZDR also cannot call the API directly from a browser. CORS requests are rejected, so calls have to go through your own back end. [2]

## Claude Code

Claude Code is covered in two ways: with API keys from a commercial organization that has Legacy ZDR, or through the separate ZDR offering for Claude Code on Claude Enterprise. Each organization has to be enabled separately; ZDR does not carry over to new organizations under the same account. [2][4]

## Through a cloud provider

On the Claude API, Claude Platform on AWS and Claude Models on Google Cloud (Anthropic managed), Anthropic is the data processor and the rules above apply. On Amazon Bedrock and Google-managed Vertex AI, the cloud provider is the processor, so their terms apply instead. See [AWS Bedrock](aws-bedrock.md) and [Google Vertex AI](google-vertex-ai.md). [2]

## How to check it

- In the Claude Console, open Settings, then Privacy Controls, and check the data retention period for the organization and each workspace. [4]
- List the features each service uses and compare them with the table above. Batch, Files and code execution are the usual gaps.
- For HIPAA workloads, keep them in a separate organization. HIPAA readiness applies to the whole organization and cannot be turned off.

## Sources

1. Anthropic Privacy Center, [How long do you store my organization's data?](https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-my-organization-s-data) Updated 1 July 2026. Accessed 25 November 2026.
2. Anthropic, [API and data retention](https://platform.claude.com/docs/en/manage-claude/api-and-data-retention). Accessed 25 November 2026.
3. Anthropic, [Enterprise Frontier Safeguards](https://platform.claude.com/docs/en/manage-claude/enterprise-frontier-safeguards/overview). Accessed 25 November 2026.
4. Anthropic Privacy Center, [I have a zero data retention agreement with Anthropic. What products does it apply to?](https://privacy.claude.com/en/articles/8956058-i-have-a-zero-data-retention-agreement-with-anthropic-what-products-does-it-apply-to) Updated 15 October 2026. Accessed 25 November 2026.

---

[← All providers](../README.md#provider-guides) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
