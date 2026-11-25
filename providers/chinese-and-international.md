# Chinese and International Providers: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#chinese--international-providers)

Many of the strongest open-weight models come from Chinese labs: DeepSeek, Qwen, GLM, Kimi, MiniMax. You can use them three ways, and each has different data terms. The lab's own API sends your data to that lab under its terms. A US or EU host runs the open weights on its own servers under its own terms. Self-hosting keeps the data with you. For sensitive data, the second or third option is usually the realistic one.

## DeepSeek API

- DeepSeek's privacy policy (last updated 10 February 2026) says it collects, processes and stores personal data in the People's Republic of China. [1]
- It keeps input data for as long as you have an account. [1]
- It uses personal data to train and improve its models and lists a right to opt out. The only channel it gives for that is email to privacy@deepseek.com. [1]
- The Open Platform (API) terms, in effect from 29 April 2026, say nothing about retention, training use or storage location for API data, and are governed by the law of mainland China. [2]

There is no ZDR option for the DeepSeek API. If you need one, use DeepSeek's open weights through a host with a documented ZDR policy, or run them yourself. The DeepSeek V4 weights are under the MIT licence. [3]

## Alibaba Cloud Model Studio (Qwen)

Alibaba publishes a data retention policy for Model Studio: [4]

- By default, prompts and responses from inference calls are kept for no more than 30 days, unless the law or service security requires longer. Stateful features such as stored conversations and files are kept until you delete them.
- Enterprise customers on the international site (alibabacloud.com) who buy inference through the API can turn on ZDR per workspace. Prompts and responses to supported models are then not written to durable storage. They pass through an encrypted cache for up to 24 hours by default and are not used for training or human review during that time.
- Alibaba does not use your inputs, outputs or files to train foundation models without your separate consent.

Model Studio has separate endpoints and API keys per region, including Singapore (`ap-southeast-1`), Frankfurt (`eu-central-1`), Virginia (`us-east-1`) and Beijing (`cn-beijing`). A key from one region does not work in another. Not every model is available in every region. [5] Pick the region that matches your residency requirement, and remember the ZDR control is described for the international site.

## Other labs

For GLM (Zhipu), Kimi (Moonshot), MiniMax and others, read the lab's own API terms before sending data to them. Their open-weight models are also offered by hosts covered in this guide. The [Together AI](together-ai.md) incident in 2026 involved requests to MiniMax M3, GLM-5.2 and Kimi K2.6 on Together's own servers, which shows that the host's practices matter as much as the model's origin.

## Your options

| Option | Who sees the data | Notes |
| :--- | :--- | :--- |
| The lab's own API | The lab, under its terms and jurisdiction | DeepSeek: no ZDR, data stored in China. Alibaba: ZDR for international enterprise customers. |
| A host running the open weights | The host | Check the host's ZDR terms, for example [Fireworks AI](fireworks-ai.md), [Together AI](together-ai.md), [Groq](groq.md) or [AWS Bedrock](aws-bedrock.md). Check the host's model catalog for the exact model. |
| A router | The router and the host it picks | [OpenRouter](openrouter.md) can limit routing to ZDR endpoints. |
| Self-hosting | Only you | See [Self-hosting open-weight models](../docs/self-hosting.md). |

## Sources

1. DeepSeek, [Privacy Policy](https://cdn.deepseek.com/policies/en-US/deepseek-privacy-policy.html). Last updated 10 February 2026. Accessed 25 November 2026.
2. DeepSeek, [Open Platform Terms of Service](https://cdn.deepseek.com/policies/en-US/deepseek-open-platform-terms-of-service.html). Effective 29 April 2026. Accessed 25 November 2026.
3. DeepSeek on Hugging Face, [DeepSeek-V4-Pro](https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro) and [DeepSeek-V4-Flash](https://huggingface.co/deepseek-ai/DeepSeek-V4-Flash) model cards (licence: MIT). Accessed 25 November 2026.
4. Alibaba Cloud, [Model Studio data retention policy](https://www.alibabacloud.com/help/en/model-studio/data-retention-policy). Accessed 25 November 2026.
5. Alibaba Cloud, [Model Studio base URL](https://www.alibabacloud.com/help/en/model-studio/base-url) and [How to obtain an API key](https://www.alibabacloud.com/help/en/model-studio/get-api-key). Accessed 25 November 2026.

---

[← All providers](../README.md#provider-guides) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
