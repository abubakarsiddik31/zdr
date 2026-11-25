# Self-Hosting Open-Weight Models

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#self-hosting-open-weight-models)

Running the model yourself is the only option where no provider is involved: no contract to trust, no retention window, no abuse monitoring. The cost is that everything the provider did is now your job: GPUs, serving, updates, monitoring, security, and the retention of your own logs.

## When it makes sense

- The data cannot go to any third party, by regulation or contract.
- You work in an air-gapped or classified environment.
- You need a specific model version to stay fixed for years.
- Your volume is high and steady enough that owning GPUs costs less than API pricing.

It usually doesn't make sense if you need the strongest frontier models, which are only available through APIs, or if your traffic is small and bursty.

A middle ground is a dedicated deployment from a provider in this guide, such as [Fireworks AI](../providers/fireworks-ai.md), [Together AI](../providers/together-ai.md) or [Hugging Face Inference Endpoints](../providers/hugging-face-inference-endpoints.md). You get isolation without running the hardware, but the provider still operates it.

## Choosing a model

Check the licence before anything else. Some popular models use custom licences with use restrictions. A sample of current open-weight models, with licences and sizes from their Hugging Face model cards: [1]

| Model | Parameters | Licence |
| :--- | :--- | :--- |
| Qwen3.5-9B | 9.7B | Apache 2.0 |
| Gemma 4 31B | 31.3B | Apache 2.0 |
| Qwen3.6-35B-A3B (MoE) | 36B total | Apache 2.0 |
| GLM-4.7-Flash | 31.2B | MIT |
| gpt-oss-20b | 20.9B | Apache 2.0 |
| gpt-oss-120b | 116.8B | Apache 2.0 |
| Mistral Large 3 | 675B | Apache 2.0 |
| DeepSeek-V4-Flash | about 300B | MIT |
| DeepSeek-V4-Pro | about 1.6T | MIT |
| Llama 4 Scout | 108.6B total | Llama 4 Community Licence |

New models come out every month. Treat this table as a starting point, and read the model card for the licence, context length and recommended serving settings.

## Serving frameworks

| Framework | Good for |
| :--- | :--- |
| [vLLM](https://docs.vllm.ai) | Production serving with many concurrent users. OpenAI-compatible API. |
| [SGLang](https://docs.sglang.ai) | High-throughput serving, structured output. OpenAI-compatible API. |
| [llama.cpp](https://github.com/ggml-org/llama.cpp) | CPUs, laptops and edge devices, with quantized GGUF models. |
| [Ollama](../providers/ollama.md) | Local development and simple single-machine use. Turn cloud features off for local-only use. |

Hugging Face's Text Generation Inference (TGI) is in maintenance mode and its repository is archived, so prefer one of the above for new deployments. [2]

A minimal vLLM server:

```bash
pip install vllm
vllm serve openai/gpt-oss-20b --port 8000

curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "openai/gpt-oss-20b", "messages": [{"role": "user", "content": "Hello"}]}'
```

## Sizing memory

A rough rule for the weights alone: parameters times bytes per parameter. That is about 2 bytes at FP16 or BF16, 1 byte at FP8 and 0.5 bytes at 4-bit. Add room for the KV cache, which grows with context length and the number of concurrent requests.

| Parameters | FP16/BF16 weights | 4-bit weights |
| :--- | :--- | :--- |
| 8B | about 16 GB | about 4 GB |
| 32B | about 64 GB | about 16 GB |
| 70B | about 140 GB | about 35 GB |
| 120B | about 240 GB | about 60 GB |
| 300B | about 600 GB | about 150 GB |

For mixture-of-experts (MoE) models, all parameters must fit in memory even though only some are active per token. Quantization saves memory but can cost quality, more so on reasoning and long-context tasks. Test on your own data before choosing.

## Hardening

- Run the server in a private subnet with no outbound internet access. Download weights through a controlled path.
- Put authentication in front of the inference port. vLLM and Ollama do not require it by default.
- Terminate TLS at a proxy or load balancer.
- Log who called which model and when, not the prompt. Your logs are now the retention problem. See [Data protection beyond ZDR](beyond-zdr.md).
- Download weights from the official repository and verify checksums. Prefer `safetensors` files over pickle-based formats, which can run code when loaded.

## Sources

1. Hugging Face model cards, accessed 25 November 2026: [Qwen3.5-9B](https://huggingface.co/Qwen/Qwen3.5-9B), [Gemma 4 31B](https://huggingface.co/google/gemma-4-31B-it), [Qwen3.6-35B-A3B](https://huggingface.co/Qwen/Qwen3.6-35B-A3B), [GLM-4.7-Flash](https://huggingface.co/zai-org/GLM-4.7-Flash), [gpt-oss-20b](https://huggingface.co/openai/gpt-oss-20b), [gpt-oss-120b](https://huggingface.co/openai/gpt-oss-120b), [Mistral Large 3](https://huggingface.co/mistralai/Mistral-Large-3-675B-Instruct-2512), [DeepSeek-V4-Flash](https://huggingface.co/deepseek-ai/DeepSeek-V4-Flash), [DeepSeek-V4-Pro](https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro), [Llama 4 Scout](https://huggingface.co/meta-llama/Llama-4-Scout-17B-16E-Instruct).
2. Hugging Face, [text-generation-inference repository](https://github.com/huggingface/text-generation-inference). Accessed 25 November 2026.

---

[← Back to the guide](../README.md) · Spotted a change in this page? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
