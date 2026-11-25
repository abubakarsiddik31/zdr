# AI Gateways and Zero Data Retention

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#other-gateways)

A gateway sits between your application and the model providers, so it sees every prompt and response. Most gateways log full request and response bodies by default, because that's what makes their dashboards useful. If you add one to a ZDR setup, turn off payload logging first, then check that the upstream providers are also on ZDR.

For OpenRouter, which is a hosted router with its own ZDR routing, see [openrouter.md](openrouter.md).

## Summary

| Gateway | Logs bodies by default? | How to stop it |
| :--- | :--- | :--- |
| Cloudflare AI Gateway | Yes, logs are on for each gateway. | Turn logs off in the gateway settings, or send `cf-aig-collect-log: false` per request. `cf-aig-collect-log-payload: false` keeps metadata but drops bodies. [1] |
| Portkey (now Prisma AIRS AI Gateway) | Yes, the log view shows full request and response objects. | Send `x-portkey-debug: false` per request ("do not track"); only tokens, cost and latency are kept. A hybrid deployment keeps logs in your VPC. [2][3] |
| LiteLLM proxy (self-hosted) | No, prompts are not stored in the spend logs unless `store_prompts_in_spend_logs` is on. Callbacks such as Langfuse get full messages unless you redact them. | Leave `store_prompts_in_spend_logs` off and set `turn_off_message_logging: true`. [4][5] |

## Cloudflare AI Gateway

- Logs are enabled by default for each gateway and can include the prompt and response. [1]
- Turn them off for the whole gateway in the dashboard under AI Gateway, Settings. Per request, `cf-aig-collect-log: false` skips the log entry, and `cf-aig-collect-log-payload: false` skips only the bodies. [1]
- Billing details are always logged for Unified Billing requests. The headers do not change what Logpush exports, so check Logpush jobs separately. [1]
- DLP policies can flag or block requests that match sensitive data profiles, and the match is recorded in the log. [1]

```bash
curl https://gateway.ai.cloudflare.com/v1/<account-id>/<gateway>/openai/chat/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "cf-aig-collect-log: false" \
  -H "Content-Type: application/json" \
  -d '{"model": "<model>", "messages": [{"role": "user", "content": "Hello"}]}'
```

## Portkey

- Per request, set the SDK's `debug` flag to `false` or send `x-portkey-debug: false`. Request and response content is left out of the logs; tokens, cost and latency are recorded. [2]
- Hosted log retention depends on plan: 3 days on Developer, 30 days on Production. [2]
- In a hybrid deployment, the gateway and the request and response log store run in your VPC; Portkey's hosted control plane gets anonymized metrics such as model, token counts and latency. Log storage location is configurable, so confirm where yours points. [3]
- The SCM deployment mode for Palo Alto Strata Cloud Manager customers is different: full request and response bodies are written to object storage in the Portkey-managed management plane. [3]

## LiteLLM proxy

LiteLLM is open source and runs in your infrastructure, so what it keeps depends on your configuration.

```yaml
general_settings:
  store_prompts_in_spend_logs: false   # the default
litellm_settings:
  turn_off_message_logging: true       # redact messages sent to logging callbacks
```

- `store_prompts_in_spend_logs` is off by default. With it off, the spend logs table records cost, tokens and identity but not content. [4]
- Logging callbacks (Langfuse, S3, OpenTelemetry and others) get message content unless `turn_off_message_logging` is on. A single request can be redacted with the `x-litellm-enable-message-redaction: true` header. [5]
- LiteLLM can mask PII before the request leaves your network. See [Data protection beyond ZDR](../docs/beyond-zdr.md).

## How to check it

- Send a test request with a recognizable string, then search the gateway's log view and any export (Logpush, S3, your observability tool) for it.
- Confirm each upstream provider is on ZDR. The gateway can't make a provider forget what it stores.

## Sources

1. Cloudflare, [AI Gateway logging](https://developers.cloudflare.com/ai-gateway/observability/logging/). Accessed 25 November 2026.
2. Portkey, [Logs](https://portkey.ai/docs/product/observability/logs). Accessed 25 November 2026.
3. Portkey, [Hybrid deployment architecture](https://portkey.ai/docs/self-hosting/hybrid-deployments/architecture) and [SCM deployment mode architecture](https://portkey.ai/docs/self-hosting/hybrid-deployments/scm-architecture). Accessed 25 November 2026.
4. LiteLLM, [Config settings](https://docs.litellm.ai/docs/proxy/config_settings). Accessed 25 November 2026.
5. LiteLLM, [Logging](https://docs.litellm.ai/docs/proxy/logging). Accessed 25 November 2026.

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
