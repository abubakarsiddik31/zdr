# Fireworks AI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · Full setup guide: [Fireworks AI ZDR: how to set it up without missing the exceptions](https://abubakar-siddik.com/guides/fireworks-ai-zero-data-retention)

Fireworks is zero retention by default for open-model inference: prompts and outputs live in memory for the request and are not written to storage. The exceptions are the Responses API (which stores conversations by default), the Batch API, the proprietary FireFunction model and features you opt into for logging. Enterprise accounts can turn on a ZDR policy that rejects anything that would store content.

## Default handling

- For open models, Fireworks does not log or store prompts or generations without an explicit opt-in. They exist only in volatile memory during the request. [1]
- With prompt caching, some prompt data and KV cache can stay in volatile memory for several minutes. It is not written to persistent storage. [1]
- Fireworks logs metadata such as token counts. [1]

## Exceptions

| Feature | What is kept | How to avoid it |
| :--- | :--- | :--- |
| Responses API | With `store=True`, the default, the prompt, response and tool calls for up to 30 days. | Pass `store=False`, or delete a stored response by its ID. [1] |
| Batch API | Needs dataset storage; ZDR does not apply. [1] | Use synchronous inference for sensitive data. |
| FireFunction (proprietary model) | Inputs and outputs logged for 30 days for analytics. [2] | Keep it off your list of allowed models. |
| FireOptimizer and similar features | Logging you explicitly opt into. [2] | Keep tuning work in a separate account or project. |

## Enforcing it

Enterprise administrators can enable a Zero Data Retention policy for the account. It rejects requests and jobs that would persist or log customer content. [1] If you are on an enterprise plan, this is the setting to turn on. It catches a missed `store=False` before any data is stored.

```python
from openai import OpenAI

client = OpenAI(base_url="https://api.fireworks.ai/inference/v1", api_key="<FIREWORKS_API_KEY>")

response = client.responses.create(
    model="accounts/fireworks/models/<open-model>",
    input="<input>",
    store=False,
)
```

## Deployment and compliance

- Serverless deployments are shared across customers. Dedicated deployments are private to your account but still run by Fireworks. The retention rules above are the same for both. [2]
- Fireworks says it is SOC 2 Type II certified, supports HIPAA and signs BAAs. Its trust center has the SOC 2 report and a HIPAA control assessment. [3][4]

## How to check it

- Confirm the account-level ZDR policy is on, if you have it.
- Search your code for `responses.create` calls without `store=False`, and for Batch API use.
- Check the model allowlist for FireFunction.

The [full setup guide](https://abubakar-siddik.com/guides/fireworks-ai-zero-data-retention) covers service accounts, data residency and the steps in more detail.

## Sources

1. Fireworks AI, [Zero data retention and data handling](https://docs.fireworks.ai/guides/security_compliance/data_handling). Accessed 25 November 2026.
2. Fireworks AI, [Models overview](https://docs.fireworks.ai/models/overview). Accessed 25 November 2026.
3. Fireworks AI, [Data security](https://docs.fireworks.ai/guides/security_compliance/data_security). Accessed 25 November 2026.
4. Fireworks AI, [Trust Center](https://trust.fireworks.ai/resources). Accessed 25 November 2026.

---

[← All providers](../README.md#-provider-guides) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
