<div align="center">

<img src="assets/banner.png" alt="Zero Data Retention (ZDR) for LLM providers" width="720">

# Zero Data Retention (ZDR) for LLM Providers

**Which LLM providers offer zero data retention, how to turn it on, and what it still doesn't cover.**

16 providers · checked against their own docs · last checked 25 November 2026

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Maintained](https://img.shields.io/badge/maintained-yes-green.svg)](https://github.com/abubakarsiddik31/zdr/graphs/commit-activity)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Web version](https://img.shields.io/badge/web_version-abubakar--siddik.com-black.svg)](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers)

[Providers](#provider-guides) · [Which approach?](#which-approach-fits) · [Topic guides](#topic-guides) · [What changed in 2026](#what-changed-in-2026)

</div>

> [!NOTE]
> Provider terms changed a lot in 2026 and will keep changing. Every page links to the provider's own documentation. Check the linked source before you rely on a detail.

## Provider guides

One page per provider: defaults, how to turn ZDR on, exceptions, how to check it, and numbered sources.

**ZDR availability:** *Default* means nothing is retained out of the box. *Self-serve* means you can turn it on yourself. *On request* means sales or an account team has to approve it. *Not available* means the provider offers no ZDR option.

### Frontier and cloud

| | Provider | Default retention | ZDR |
| :-: | :--- | :--- | :--- |
| <img src="assets/logos/openai.png" width="24" alt=""> | [**OpenAI**](providers/openai.md) | Abuse logs up to 30 days | On request |
| <img src="assets/logos/anthropic.png" width="24" alt=""> | [**Anthropic**](providers/anthropic.md) | Deleted within 30 days | On request |
| <img src="assets/logos/google-vertex-ai.png" width="24" alt=""> | [**Google Vertex AI**](providers/google-vertex-ai.md) | 24h cache; flagged prompts up to 90 days | On request |
| <img src="assets/logos/azure-openai.png" width="24" alt=""> | [**Azure OpenAI**](providers/azure-openai.md) | Flagged content can be stored for review | On request, ending for models from 1 Nov 2026 |
| <img src="assets/logos/aws-bedrock.png" width="24" alt=""> | [**AWS Bedrock**](providers/aws-bedrock.md) | Most models keep nothing; some keep up to 30 days | Self-serve |
| <img src="assets/logos/cohere.png" width="24" alt=""> | [**Cohere**](providers/cohere.md) | 30 days; training is opt-out | On request |

### Open-model inference

| | Provider | Default retention | ZDR |
| :-: | :--- | :--- | :--- |
| <img src="assets/logos/fireworks-ai.png" width="24" alt=""> | [**Fireworks AI**](providers/fireworks-ai.md) | Nothing stored for open models | Default |
| <img src="assets/logos/together-ai.png" width="24" alt=""> | [**Together AI**](providers/together-ai.md) | ZDR for new organizations | Self-serve |
| <img src="assets/logos/groq.png" width="24" alt=""> | [**Groq**](providers/groq.md) | Logs up to 30 days | Self-serve |
| <img src="assets/logos/mistral-ai.png" width="24" alt=""> | [**Mistral AI**](providers/mistral-ai.md) | 30 rolling days | On request |
| <img src="assets/logos/hugging-face.png" width="24" alt=""> | [**Hugging Face**](providers/hugging-face-inference-endpoints.md) | No payloads stored; logs 30 days | Default |
| <img src="assets/logos/ollama.png" width="24" alt=""> | [**Ollama**](providers/ollama.md) | Local: stays on your machine; Cloud: not logged | Default |

### Gateways and others

| | Provider | Default retention | ZDR |
| :-: | :--- | :--- | :--- |
| <img src="assets/logos/openrouter.png" width="24" alt=""> | [**OpenRouter**](providers/openrouter.md) | Prompts not logged unless you opt in | Self-serve |
| | [**Other AI gateways**](providers/gateways.md) | Depends on the gateway | Depends |
| <img src="assets/logos/replicate.jpg" width="24" alt=""> | [**Replicate**](providers/replicate.md) | API predictions deleted after 1 hour | Not available |
| <img src="assets/logos/deepseek.png" width="24" alt=""> | [**DeepSeek API**](providers/chinese-and-international.md) | Kept while the account exists, stored in China | Not available |
| <img src="assets/logos/alibaba.png" width="24" alt=""> | [**Alibaba Model Studio**](providers/chinese-and-international.md) | Up to 30 days | On request |

## Which approach fits

```mermaid
flowchart TD
    Start(["Sensitive data in prompts"]) --> Q1{"Can any third party<br/>see the data?"}
    Q1 -->|No| SH["Self-host open weights"]
    Q1 -->|Yes| Q2{"Need the strongest<br/>frontier models?"}
    Q2 -->|Yes| Q3{"Regulated data<br/>(PHI, federal)?"}
    Q3 -->|Yes| Cloud["Cloud AI service with a BAA<br/>or FedRAMP authorization"]
    Q3 -->|No| Direct["Provider API with ZDR"]
    Q2 -->|No| Open["Open-model host with ZDR"]
    Direct --> GW{"Several providers?"}
    Open --> GW
    GW -->|Yes| Router["Gateway with logging off,<br/>ZDR providers only"]
```

| Approach | Who can see the data | Models | Effort |
| :--- | :--- | :--- | :--- |
| [Self-hosted](docs/self-hosting.md) | Only you | Open weights | High |
| [Cloud AI service, private network](docs/architectures.md#1-a-cloud-provider-over-a-private-network) | The cloud provider, under contract | Frontier and open | Medium |
| Provider API with ZDR | The provider, during the request | That provider's models | Low |
| Open-model host with ZDR | The host, during the request | Open weights | Low |
| [Gateway](providers/gateways.md) | The gateway and each provider | Many | Low to medium |

## Topic guides

| Guide | What it covers |
| :--- | :--- |
| [Threat model](docs/threat-model.md) | What ZDR protects against, and where your prompts actually go |
| [Self-hosting](docs/self-hosting.md) | When to do it, models, frameworks, hardware sizing, hardening |
| [Compliance](docs/compliance.md) | HIPAA (BAAs), SOC 2, GDPR, FedRAMP |
| [Beyond ZDR](docs/beyond-zdr.md) | PII redaction, proxies, your own logging, prompt injection |
| [Verification](docs/verification.md) | The evidence an auditor will ask for |
| [Architectures](docs/architectures.md) | Private networking, self-hosted stacks, gateways, a HIPAA reference design |

## What changed in 2026

- New frontier models now require retention. Anthropic's Claude Fable and Mythos lines need 30-day retention unless you use Enterprise Frontier Safeguards. Bedrock and Vertex AI apply similar rules.
- AWS Bedrock replaced "nothing is stored" with an explicit retention mode. Set it to `none` yourself.
- Microsoft is ending Modified Abuse Monitoring for Azure models released on or after 1 November 2026.
- OpenAI added Private Safety Processing, and data residency now covers ten regions.
- Together AI disclosed that stored prompt samples were downloaded by an unauthorized party, and made ZDR the default for new organizations.

## In-depth guides

Longer write-ups on my site:

- [Zero Data Retention (ZDR) for LLM Providers](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers): the full guide in one page, with a contents panel
- [Fireworks AI ZDR: how to set it up without missing the exceptions](https://abubakar-siddik.com/guides/fireworks-ai-zero-data-retention)

More are listed at [abubakar-siddik.com/guides](https://abubakar-siddik.com/guides).

## Maintainer

Maintained by [Abu Bakar Siddik](https://abubakar-siddik.com/about), an AI engineer who builds private LLM systems. I check providers' terms when they change and write up the tricky ones in more depth.

Hearing about real setups keeps this reference accurate. If you hit an edge case these pages don't answer, [open an issue](https://github.com/abubakarsiddik31/zdr/issues) or [write to me](mailto:theabubakarsiddik@gmail.com?subject=ZDR%20question).

## Contributing

Corrections and new providers are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md). Please link the official source for every claim, note the date you checked it, and test any code before submitting.

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE).
