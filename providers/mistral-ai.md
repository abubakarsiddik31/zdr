# Mistral AI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#mistral-ai)

Mistral keeps API inputs and outputs for 30 rolling days to monitor abuse. ZDR removes that window for stateless endpoints, but you have to request it, give a reason, and be on pay-as-you-go. Training is a separate setting. Data sent to Labs and Public Preview models can be used for training whatever you set, so keep sensitive data away from them.

## Default handling

- Apart from some specific APIs, Mistral keeps API inputs and outputs for as long as it takes to generate the output, then for 30 rolling days to monitor abuse, unless ZDR is on. [1]
- Agents API inputs and outputs are kept until you close your account. Fine-tuning data is kept until you delete it or close your account. [1]
- On the free mode of Mistral Studio, Mistral may use your inputs and outputs to train its models. You can opt out. [2]
- On pay-as-you-go you control training use and can opt out at any time. The help center does not say which way the setting starts, so check it. [2]
- Enterprise customers are opted out of training by default. [2]
- On Labs and Public Preview models, your data may be used for training and the opt-out does not apply, whatever your plan. [2]

## Getting ZDR

From Mistral's ZDR article: [3]

- Only on pay-as-you-go. Free accounts have to switch to pay-as-you-go first.
- Not self-serve. Request it through the Help Center or support, with enough detail about your legitimate reason. Mistral reviews each request and may approve or deny it.
- Once approved, it shows in the Admin panel under Privacy.

With ZDR on, API inputs and outputs are not kept beyond what is needed to return the response.

## What ZDR covers

Stateless endpoints, across models except Labs and Public Preview models: [3]

`/v1/chat/completions`, `/v1/fim/completions`, `/v1/embeddings`, the moderation and classification endpoints, `/v1/ocr`, `/v1/audio/speech`, `/v1/audio/transcriptions`

Not covered: Agents, Conversations, Libraries, Batch files, `/v1/files`, Vibe Work, Chat, and traces sent to Studio Observability. [3][4]

## Turning off training

ZDR and the training opt-out are separate controls. [3] For the API: in the Admin panel, open Privacy and turn off "Data usage for improving our services". The Vibe toggle is separate, so set both if you use both. [5]

## Through Microsoft

For Mistral models used through Microsoft, Mistral's terms say it will not use customer data or outputs to train its models. [4]

## Self-hosting

Several Mistral models have open weights under Apache 2.0 and can run on your own hardware, which removes the provider from the data path. See [Self-hosting open-weight models](../docs/self-hosting.md).

## How to check it

- In the Admin panel under Privacy, confirm ZDR shows as approved and the training toggle is off. Screenshot both.
- Make sure no sensitive traffic goes to Labs or Public Preview models.
- Search your code for Agents, Conversations, Batch and Files API calls, which ZDR does not cover.

## Sources

1. Mistral AI, [Privacy Policy](https://legal.mistral.ai/terms/privacy-policy). Accessed 25 November 2026.
2. Mistral AI Help Center, [Do you use my user data to train your artificial intelligence models?](https://help.mistral.ai/en/articles/347617-do-you-use-my-user-data-to-train-your-artificial-intelligence-models) Accessed 25 November 2026.
3. Mistral AI Help Center, [Can I activate Zero Data Retention (ZDR)?](https://help.mistral.ai/en/articles/347612-can-i-activate-zero-data-retention-zdr) Accessed 25 November 2026.
4. Mistral AI, [Zero data retention](https://docs.mistral.ai/admin/monitor-comply/zero-data-retention) and [Mistral Terms of Use for Microsoft](https://legal.mistral.ai/terms/terms-of-service-dpa-microsoft/). Accessed 25 November 2026.
5. Mistral AI Help Center, [Can I opt out of my input or output data being used for training?](https://help.mistral.ai/en/articles/455207-can-i-opt-out-of-my-input-or-output-data-being-used-for-training) Accessed 25 November 2026.

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
