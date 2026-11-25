# Self-Hosting Open-Weight Models

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#self-hosting-open-weight-models)

Self-hosting gives you the **strongest privacy guarantee**: data never leaves your infrastructure. No contracts, no trust required, no retention windows.

## When to Self-Host

- You're in an air-gapped or classified environment
- Regulatory requirements prohibit sending data to any third party
- You need full control over model behavior and infrastructure
- You're cost-sensitive at high volume (break-even vs. API pricing at ~1M+ tokens/day)

## Trade-offs

- **Quality gap**: Open-weight models trail frontier models (GPT-4o, Claude Opus, Gemini Pro) on complex reasoning
- **Operational burden**: GPU procurement, driver management, model updates, monitoring
- **No built-in safety filters**: You're responsible for content moderation

## Top Open-Weight Models for Self-Hosting

| Model | Parameters | Architecture | Min Hardware (Quantized) | License |
| :--- | :--- | :--- | :--- | :--- |
| **Llama 4 Scout** | 17B active / 109B total | MoE (16 experts) | 1x H100 80GB (INT4) | Llama License |
| **Llama 4 Maverick** | 17B active / 400B total | MoE (128 experts) | 1x H100 host | Llama License |
| **DeepSeek-R1** | 671B | MoE | 8-16x H100 (FP8) | MIT |
| **DeepSeek-R1-Distill-Qwen-32B** | 32B | Dense | 1x A100 40GB (INT4) | MIT |
| **Mistral Large 3** | 41B active / 675B total | MoE | 8x H100 | Apache 2.0 |
| **Qwen 3.5** | Various (0.6B-72B+) | Dense + MoE | Varies | Apache 2.0 |
| **Qwen3-32B** | 32B | Dense | 1x A100 40GB (INT4) | Apache 2.0 |

## Inference Frameworks

| Framework | Best For | Key Feature |
| :--- | :--- | :--- |
| **vLLM** | Production serving, high concurrency | PagedAttention (40%+ less memory fragmentation), ~19x throughput vs. Ollama |
| **Ollama** | Local dev, simple deployment | One-command setup, auto-quantization, OpenAI-compatible API |
| **llama.cpp** | CPU inference, edge devices | Runs on consumer hardware without GPU |
| **SGLang** | High-throughput structured generation | Fast constrained decoding |
| **TGI** (HuggingFace) | HF model ecosystem integration | Native HF model support, production-ready |

## Quick Start: vLLM

```bash
pip install vllm

# Serve a model with OpenAI-compatible API
vllm serve deepseek-ai/DeepSeek-R1-Distill-Qwen-32B \
  --tensor-parallel-size 1 \
  --gpu-memory-utilization 0.8 \
  --enforce-eager \
  --port 8000

# Call it like OpenAI
curl http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "deepseek-ai/DeepSeek-R1-Distill-Qwen-32B",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

## Quick Start: Ollama

```bash
# Install and run in one command
curl -fsSL https://ollama.com/install.sh | sh
ollama run llama4-scout

# Or serve with OpenAI-compatible API
ollama serve &
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama4-scout",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

## Hardware Sizing Guide

| Model Size | VRAM (FP16) | VRAM (INT4) | Recommended GPU | System RAM |
| :--- | :--- | :--- | :--- | :--- |
| 7B | ~14 GB | ~4 GB | 1x RTX 3080/4090 | 16 GB |
| 13B | ~26 GB | ~7 GB | 1x RTX 4090 / A100 | 32 GB |
| 32B | ~64 GB | ~18 GB | 1x A100 40GB / H100 | 64 GB |
| 70B | ~140 GB | ~38 GB | 2x A100 80GB / 1x H100 | 128 GB |
| 400B+ (MoE) | ~800 GB | ~200 GB | 8x H100 | 512 GB |
| 671B (DeepSeek-R1) | ~1.3 TB | ~340 GB | 8-16x H100 (FP8) | 1 TB |

> **Quantization sweet spot**: Q4_K_M retains ~95% of full-precision quality while cutting memory by ~4x. For reasoning models (DeepSeek-R1), prefer FP8 or higher — quantization artifacts hurt reasoning accuracy disproportionately.

## Security Hardening for Self-Hosted

- **Network isolation**: Deploy in a private VPC/subnet with no internet egress. Use security groups to restrict access to your application layer only
- **Authentication**: Put an auth proxy (e.g., OAuth2 Proxy, Envoy with JWT validation) in front of the inference endpoint
- **TLS**: Terminate TLS at a load balancer or reverse proxy. Never expose the inference port directly
- **Audit logging**: Log request metadata (who, when, which model) without logging prompt content
- **Model provenance**: Verify model checksums from official sources. Don't download from untrusted mirrors

---

[← Back to the guide](../README.md) · Spotted a change in this page? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
