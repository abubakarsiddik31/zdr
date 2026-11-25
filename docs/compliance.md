# Compliance Mapping: HIPAA, GDPR, FedRAMP, SOC 2

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#compliance-mapping)

ZDR and compliance overlap but are different things. ZDR is about whether the provider keeps your data. A regulation asks whether you have the right contract, controls and evidence. You can be HIPAA-compliant with a provider that retains data under a BAA, and you can have ZDR and still fail a GDPR review because the data left the region. This page maps the common regimes to what you need from an LLM provider.

This is engineering guidance, not legal advice. Your compliance team and your contracts decide.

## Start with the data

| Data you send | Main question | What you need from the provider |
| :--- | :--- | :--- |
| Protected health information (US) | HIPAA | A signed BAA that names the service, and use of only the features it covers. |
| Personal data of people in the EU | GDPR | A data processing agreement, a lawful transfer basis, and often in-region processing. |
| US federal data | FedRAMP | A service and model authorized at the right level, deployed in the authorized environment. |
| Payment card numbers | PCI DSS | Usually: don't send them. Tokenize before the model sees anything. |
| Anything confidential | Your own policy and SOC 2 | ZDR or a short retention window, a SOC 2 report, and a vendor risk review. |

## HIPAA

You need a Business Associate Agreement (BAA) before sending PHI. A BAA usually covers named services and features, not the whole platform, so check the list as well as the signature.

| Provider | BAA | What to know |
| :--- | :--- | :--- |
| OpenAI API | For eligible customers. | Needs an executed BAA and a "Modified Retention" setup (Modified Abuse Monitoring, ZDR, Safety Retention or the former Eyes Off). Only listed endpoints are eligible. Live web search is not covered. [1][2] |
| Anthropic API | Self-serve in the Console, or negotiated through sales. | HIPAA readiness is its own arrangement and replaces ZDR for PHI. It is permanent for the organization and blocks non-eligible features with a `400`. Not available on Claude Platform on AWS or in Claude Code. [3] |
| Azure OpenAI | Covered under Microsoft's HIPAA BAA for in-scope Azure services. | Check the in-scope list for the services you use. [4] |
| AWS Bedrock | Bedrock is HIPAA eligible under the AWS BAA. | Check the HIPAA eligible services reference. [5][6] |
| Google Vertex AI | Generative AI on Vertex AI is covered under Google Cloud's BAA. | Check the covered products list for the features you use. [7] |
| Fireworks AI | Signs BAAs. | Says it is SOC 2 Type II certified and supports HIPAA. [8] |
| Self-hosted | Not applicable to a vendor. | Your own infrastructure has to meet the HIPAA Security Rule. |

A BAA makes a provider eligible. It does not make your system compliant. Access controls, audit logs, encryption and your own logging still matter. Read [Data protection beyond ZDR](beyond-zdr.md) for the parts on your side.

For provider details: [OpenAI](../providers/openai.md), [Anthropic](../providers/anthropic.md), [Azure OpenAI](../providers/azure-openai.md), [AWS Bedrock](../providers/aws-bedrock.md), [Google Vertex AI](../providers/google-vertex-ai.md), [Fireworks AI](../providers/fireworks-ai.md).

## GDPR

- A provider processing personal data for you is a processor. Article 28 requires a contract (a DPA) that sets out what they may do with it. [9]
- Transfers outside the EEA need a legal basis, such as standard contractual clauses. Groq, for example, stores customer data in the US and offers SCCs. [10]
- In-region processing helps. Options include OpenAI's EU data residency endpoint [11], Azure's EU Data Zone deployments [12], Alibaba Cloud's Frankfurt region [13], OpenRouter's EU in-region routing [14], and Together AI's EU VPC deployments [15].
- Check what in-region covers. OpenAI says system data such as billing and usage statistics can still leave the region [11]. Together's US geo-inference covers where requests are processed, not where data is stored or logged [15].
- ZDR helps with erasure requests, since there is less to erase. It doesn't replace a DPA.

## FedRAMP

FedRAMP authorizations apply to a service in a specific environment, and increasingly to specific models. FedRAMP's 2026 consolidated rules renamed the baselines: High is now Class D and Moderate is Class C. [16]

| Service | Status (check the live list before relying on it) |
| :--- | :--- |
| AWS Bedrock | FedRAMP High in AWS GovCloud (US). Models are approved individually; AWS announced OpenAI GPT and GPT OSS models at FedRAMP High and DoD IL4/IL5 in GovCloud in June 2026. [6][17] |
| Azure OpenAI | FedRAMP High in Azure Government, also DoD IL4, IL5 and IL6. [18] |
| Google Vertex AI (Generative AI) | FedRAMP High and DoD IL4, now listed as Generative AI on Gemini Enterprise Agent Platform. [19] |

## SOC 2

A SOC 2 Type II report tells you a provider's controls were tested over a period. It says nothing about ZDR by itself, and it covers the scope the auditor tested, which may not include every product. Get the report from the provider's trust center and check the scope and the exceptions section. Providers in this guide that state they have one include Fireworks AI [8], Hugging Face [20] and Cohere [21].

## PCI DSS

Don't send card numbers to a model. Tokenize or mask them before the prompt is built, so the LLM path stays out of scope for cardholder data. [Data protection beyond ZDR](beyond-zdr.md) covers redaction before the API call.

## Sources

1. OpenAI Help Center, [HIPAA eligible products and functionality](https://help.openai.com/en/articles/20001069-hipaa-eligible-products-and-functionality). Accessed 25 November 2026.
2. OpenAI, [Data controls in the OpenAI platform](https://developers.openai.com/api/docs/guides/your-data). Accessed 25 November 2026.
3. Anthropic, [API and data retention](https://platform.claude.com/docs/en/manage-claude/api-and-data-retention). Accessed 25 November 2026.
4. Microsoft Learn, [HIPAA and the HITECH Act](https://learn.microsoft.com/en-us/azure/compliance/offerings/offering-hipaa-us). Accessed 25 November 2026.
5. AWS, [HIPAA eligible services reference](https://aws.amazon.com/compliance/hipaa-eligible-services-reference/). Accessed 25 November 2026.
6. AWS, [Amazon Bedrock security and compliance](https://aws.amazon.com/bedrock/security-compliance/). Accessed 25 November 2026.
7. Google Cloud, [HIPAA compliance on Google Cloud](https://cloud.google.com/security/compliance/hipaa). Accessed 25 November 2026.
8. Fireworks AI, [Data security](https://docs.fireworks.ai/guides/security_compliance/data_security). Accessed 25 November 2026.
9. [GDPR Article 28, Processor](https://gdpr-info.eu/art-28-gdpr/). Accessed 25 November 2026.
10. Groq, [Your data in GroqCloud](https://console.groq.com/docs/your-data). Accessed 25 November 2026.
11. OpenAI, [Data residency](https://developers.openai.com/api/docs/guides/data-residency). Accessed 25 November 2026.
12. Microsoft Learn, [Data, privacy, and security for Foundry Models sold by Azure](https://learn.microsoft.com/en-us/azure/foundry/responsible-ai/openai/data-privacy). Accessed 25 November 2026.
13. Alibaba Cloud, [Model Studio base URL](https://www.alibabacloud.com/help/en/model-studio/base-url). Accessed 25 November 2026.
14. OpenRouter, [Provider logging](https://openrouter.ai/docs/guides/privacy/provider-logging). Accessed 25 November 2026.
15. Together AI, [Privacy and security](https://docs.together.ai/docs/privacy-and-security). Accessed 25 November 2026.
16. AWS, [FedRAMP compliance](https://aws.amazon.com/compliance/fedramp/). Accessed 25 November 2026.
17. AWS, [OpenAI GPT, OpenAI GPT OSS, and NVIDIA Nemotron models on Amazon Bedrock receive FedRAMP High and DoD IL-4/5 approval in AWS GovCloud (US)](https://aws.amazon.com/about-aws/whats-new/2026/06/addl-bedrock-model-fedramp-il-5-govcloud/). June 2026. Accessed 25 November 2026.
18. Microsoft Learn, [Azure services in FedRAMP audit scope](https://learn.microsoft.com/en-us/azure/azure-government/compliance/azure-services-in-fedramp-auditscope). Accessed 25 November 2026.
19. Google Cloud, [FedRAMP and DoD compliance scope](https://docs.cloud.google.com/docs/security/compliance/fedramp-dod-compliance-scope). Accessed 25 November 2026.
20. Hugging Face, [Inference Endpoints security and compliance](https://huggingface.co/docs/inference-endpoints/en/security). Accessed 25 November 2026.
21. Cohere, [Enterprise data commitments](https://cohere.com/enterprise-data-commitments). Accessed 25 November 2026.

---

[← Back to the guide](../README.md) · Spotted a change in this page? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
