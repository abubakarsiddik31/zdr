# Groq: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#groq)

Groq does not keep inference data by default, but it can keep logs for troubleshooting and abuse investigation for up to 30 days. Turning on Zero Data Retention in the console removes that window. It is self-serve, but it also turns off Batch and fine-tuning, because those need stored data.

## Default handling

From Groq's "Your data" page: [1]

- Groq does not retain customer data for inference requests by default. Usage metadata, which does not include inputs or outputs, is always collected.
- Inference logs kept for troubleshooting or abuse investigation are retained for up to 30 days, unless the law requires longer.
- Batch input and output files are kept for 30 days unless you delete them first.
- Fine-tuning datasets and model weights are kept until you delete them.
- Customer data is stored in Google Cloud buckets in the United States. For transfers from other countries, Groq offers standard contractual clauses.

## Turning on ZDR

Any customer can turn it on from the Data Controls page in the Groq console. Organization admins can turn it on for the whole organization or for individual features. [1]

With ZDR on, Groq does not retain inference data for system reliability or abuse monitoring. Features that need stored data are disabled, including Batch processing and fine-tuning. [1]

## Compliance

The "Your data" page does not cover certifications or BAAs. Groq points to its [Trust Center](https://trust.groq.com) for security standards; check there for current SOC 2 and HIPAA documents. [1]

## How to check it

- In the console, open Data Controls and confirm ZDR is on at the organization level. Screenshot it.
- Confirm Batch and fine-tuning calls now fail. If they still work, ZDR is only on for some features.
- Delete any Batch files and fine-tuning datasets left from before.

## Sources

1. Groq, [Your data in GroqCloud](https://console.groq.com/docs/your-data). Accessed 25 November 2026.

---

[← All providers](../README.md#-provider-guides) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
