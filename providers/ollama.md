# Ollama: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026

Ollama is two things now. Run locally, it is software on your own machine and Ollama never sees your prompts. Ollama Cloud runs models on Ollama's hosted compute, and Ollama says it does not store, log or train on prompts and responses there. The risk is mixing the two by accident, since cloud models can run through the same local install. If data must stay on your hardware, turn cloud features off.

## Local Ollama

- Ollama says it does not see your prompts or data when you run locally. [1]
- The server listens on `127.0.0.1:11434` by default. Setting `OLLAMA_HOST` to another address exposes it to the network, and then you need your own authentication in front of it. [1]
- To run in local-only mode, set `OLLAMA_NO_CLOUD=1`, or set `disable_ollama_cloud` to `true` in `~/.ollama/server.json`, then restart. This also removes access to cloud models and web search. [1]

```bash
OLLAMA_NO_CLOUD=1 ollama serve
```

## Ollama Cloud

From Ollama's cloud FAQ and pricing pages: [2][3]

- Ollama processes prompts and responses transiently and does not store or log that content, and never trains on it.
- It collects account information and usage metadata that does not include prompt or response content.
- Models run mainly in the United States, and requests may be routed to Europe for extra capacity.
- Ollama works with NVIDIA Cloud Providers to host models and says it requires no logging, no training and zero retention from them.

These are Ollama's own statements. Unlike the larger clouds, Ollama doesn't point to a SOC 2 report or BAA on these pages. If you need that level of assurance, ask Ollama for it or use a provider that publishes one.

## How to check it

- For local-only use, confirm `OLLAMA_NO_CLOUD=1` or `disable_ollama_cloud` is set on every machine, and that pulling a `:cloud` model fails.
- Confirm the server isn't bound to a public interface unless you meant it to be.
- If you use Ollama Cloud, record which models run there, and keep the account separate from local-only machines.

For running open models in production, see [Self-hosting open-weight models](../docs/self-hosting.md).

## Sources

1. Ollama, [FAQ](https://docs.ollama.com/faq). Accessed 25 November 2026.
2. Ollama, [Cloud FAQ](https://docs.ollama.com/cloud/faq). Accessed 25 November 2026.
3. Ollama, [Pricing](https://ollama.com/pricing). Accessed 25 November 2026.

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
