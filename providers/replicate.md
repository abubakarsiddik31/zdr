# Replicate: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#replicate)

Replicate has no ZDR setting. Predictions made through the API are deleted after an hour by default, which is short, but it is still retention. Predictions made in the web interface are kept indefinitely. Replicate's published terms do not say clearly whether customer inputs can be used beyond running your request, so ask for that in writing before sending sensitive data.

## Default handling

From Replicate's data retention page: [1]

- For predictions created through the API, inputs, outputs, output files and logs are removed automatically after an hour. Save your own copies first if you need them; webhooks are the usual way.
- Predictions created through the web interface are kept indefinitely. You can delete one from its page in the dashboard, which removes it and its files.
- The page does not describe a way to delete an API prediction sooner.

## Terms

- Section 5.2 of the terms of service gives Replicate a licence to use "Customer Data" (your inputs and outputs) as far as needed to provide the output, train and generate your own derivative models, provide the service, and "create and compile Resultant Data". [2]
- The privacy policy describes Replicate as a processor or service provider when it handles customers' personal information. [3]
- Neither document says plainly that Replicate will not train its own models on your data. If that matters for your use, get it confirmed in your contract.

## When it fits

Replicate suits workloads where a one-hour window is acceptable, such as images or non-sensitive text. For regulated data, use a provider with a documented ZDR setting, or run the model yourself. See [Self-hosting open-weight models](../docs/self-hosting.md).

## How to check it

- Make sure all production traffic uses the API, not the web interface.
- Store outputs you need through webhooks, and make sure your webhook endpoint does not log request bodies.
- Ask Replicate in writing about training use and what "Resultant Data" covers.

## Sources

1. Replicate, [Data retention](https://replicate.com/docs/topics/predictions/data-retention). Accessed 25 November 2026.
2. Replicate, [Terms of service](https://replicate.com/terms), section 5.2. Accessed 25 November 2026.
3. Replicate, [Privacy policy](https://replicate.com/privacy). Accessed 25 November 2026.

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
