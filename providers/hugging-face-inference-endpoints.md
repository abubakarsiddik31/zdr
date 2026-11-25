# Hugging Face Inference Endpoints: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#hugging-face-inference-endpoints)

Inference Endpoints are dedicated deployments of a model you pick, run by Hugging Face on a public cloud. Hugging Face does not store request payloads or tokens, but it keeps logs for 30 days. Inference Providers, the serverless router on the Hub, is a different product: Hugging Face stores nothing, but the request goes to a third-party provider with its own policy.

## Inference Endpoints

- Hugging Face does not store any customer payloads or tokens passed to an endpoint. [1]
- Logs are stored for 30 days. [1]
- Traffic is encrypted with TLS. [1]

Three security levels: [1]

| Level | Access |
| :--- | :--- |
| Public | Reachable from the internet over TLS, no authentication. |
| Protected | Reachable from the internet over TLS, needs a Hugging Face token. |
| Private | Only through an intra-region AWS or Azure PrivateLink connection. Not reachable from the internet. |

For sensitive data, use Private. You give Hugging Face the AWS account ID that should have access. [1]

Make sure your own application does not log what Hugging Face does not. Custom handlers and container images that you deploy run your code, and anything they print can end up in the 30-day logs.

## Inference Providers

When you send a request through Inference Providers, Hugging Face does not store the request body or response, and does not keep user data for training. Debug logs are kept for up to 30 days without user data or tokens. [2]

The request is served by an external provider, and that provider's data policy applies. [2] Check the provider you route to. For some of them, see [Fireworks AI](fireworks-ai.md), [Together AI](together-ai.md) and [Groq](groq.md).

## Compliance

- The Hugging Face Hub and Inference Endpoints are SOC 2 Type 2 certified. [1]
- A GDPR data processing agreement is available with an Enterprise Hub subscription. [1]
- The security page does not mention HIPAA or BAAs. [1]

## How to check it

- Confirm every endpoint that handles sensitive data is Private, and that public network access isn't needed.
- Review custom handlers and containers for logging of request bodies.
- If you use Inference Providers, list which providers your requests go to and check each one's policy.

## Sources

1. Hugging Face, [Inference Endpoints security and compliance](https://huggingface.co/docs/inference-endpoints/en/security). Accessed 25 November 2026.
2. Hugging Face, [Inference Providers security and compliance](https://huggingface.co/docs/inference-providers/en/security). Accessed 25 November 2026.

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
