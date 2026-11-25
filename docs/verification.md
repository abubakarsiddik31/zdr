# Verifying and Auditing a ZDR Setup

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#verification--audit-guide)

"We have ZDR" is a claim. An auditor will want evidence: the contract, the setting, proof that your code stays inside what the setting covers, and proof that your own systems don't keep what the provider doesn't. This page lists the evidence to collect, in four groups.

## 1. Contracts

- The ZDR approval, addendum or written confirmation, naming the organization, account or project it covers.
- A BAA if you process PHI, with the list of covered services. See [Compliance mapping](compliance.md).
- A DPA for personal data under GDPR.
- The provider's SOC 2 Type II report, with its scope and exceptions read.
- For programs that move safety review to your side (Anthropic Enterprise Frontier Safeguards, OpenAI Private Safety Processing), the storage and review obligations you accepted.

## 2. Configuration

Capture the setting itself, with the date. Commands and screens from the provider pages:

| Provider | What to capture |
| :--- | :--- |
| OpenAI | Settings, Organization, Data controls, Data Retention, for the organization and each project. [OpenAI](../providers/openai.md) |
| Anthropic | The data retention period under Settings, Privacy Controls, for the organization and each workspace. [Anthropic](../providers/anthropic.md) |
| Azure OpenAI | The `ContentLogging` capability set to `false` on the resource. [Azure OpenAI](../providers/azure-openai.md) |
| AWS Bedrock | The data retention mode for each Region and project, and each model's `allowed_modes`. [AWS Bedrock](../providers/aws-bedrock.md) |
| Google Vertex AI | The project's `cacheConfig` with `disableCache: true`, plus the abuse-logging exemption. [Google Vertex AI](../providers/google-vertex-ai.md) |
| Groq, Together AI, Mistral | The Data Controls or Privacy page showing ZDR on. |
| OpenRouter | Privacy settings with ZDR on for each model group, and prompt logging off. [OpenRouter](../providers/openrouter.md) |

For AWS Bedrock, for example:

```bash
curl https://bedrock-mantle.us-east-1.api.aws/v1/data_retention -H "x-api-key: $BEDROCK_API_KEY"
# expected: {"mode": "none", ...}

aws bedrock get-model-invocation-logging-configuration
# expected: no logging configuration, unless you chose to log to your own account
```

For Azure OpenAI:

```bash
az cognitiveservices account show --name <resource> --resource-group <rg> \
  --query "properties.capabilities[?name=='ContentLogging']"
# expected: a ContentLogging entry with value "false"
```

## 3. Code paths

The setting only covers some endpoints and features. Show that your code stays inside them.

- List every provider endpoint and feature your services call. Search the code for the usual exceptions: batch jobs, file uploads, assistants and agents, conversation or thread storage, code execution, background mode, MCP connectors.
- Where a `store` parameter exists, show it is set to `false` in code, not left to the default.
- Show which API keys belong to which project or organization, so a key from a project without ZDR can't be used by mistake.
- Where you can, enforce it: Bedrock's SCP condition keys, OpenRouter's per-request `zdr`, Fireworks' enterprise ZDR policy, Anthropic's HIPAA feature blocking.

## 4. Your own environment

Send a test request with a unique marker string, then search for it everywhere it could land:

- Application logs, at the log level used in production.
- Gateway and proxy logs, and their exports (Logpush, S3 and similar).
- Error tracking, including events created from failed requests.
- LLM tracing and evaluation tools.
- Databases and caches that store chat history.
- Analytics and session replay in the browser.

Record where the marker appeared and what you changed. [Data protection beyond ZDR](beyond-zdr.md) lists the usual places and fixes.

## Keep it current

Provider terms changed a lot in 2026. Anthropic, Azure and Bedrock all changed how retention works for some models. Re-check the provider pages and your settings on a schedule, and whenever you adopt a new model, because some new models come with their own retention requirements.

---

[← Back to the guide](../README.md) · Spotted a change in this page? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
