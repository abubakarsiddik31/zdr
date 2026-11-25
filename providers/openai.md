# OpenAI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#openai)

OpenAI does not train on API data unless you opt in. By default it keeps abuse monitoring logs for up to 30 days. Zero Data Retention removes your content from those logs and stops eligible endpoints from storing responses, but it needs OpenAI's approval and it does not cover every endpoint or tool.

## Default retention

- API data is not used to train OpenAI models unless you explicitly opt in. This has been the policy since 1 March 2023. [1]
- Abuse monitoring logs keep customer content for up to 30 days, unless the law requires longer or OpenAI needs it to protect its services or others from harm. [1]
- Endpoints that store application state (Responses with `store: true`, Conversations, Assistants, Files, vector stores and so on) keep that state until you delete it. [1]

## Retention options

OpenAI offers several controls, all subject to its approval. You request them through sales. [1]

| Option | What it does |
| :--- | :--- |
| Modified Abuse Monitoring (MAM) | Excludes customer content from abuse monitoring logs on all endpoints. Rare exceptions apply to image and file inputs. Stored application state still works. |
| Zero Data Retention (ZDR) | Same exclusion as MAM, and `store` on `/v1/responses` and `/v1/chat/completions` is always treated as `false`, even if a request sets it to `true`. |
| ZDR with Private Safety Processing (PSP) | ZDR plus automated safety review. Selected records are encrypted and written to a storage bucket you own with a 30-day lifecycle; human access is disabled in the review runtime. [2] |
| Private Retention with PSP | Formerly "Eyes Off". Content stays in encrypted abuse monitoring logs on OpenAI's side but is excluded from human review unless the law requires it. [1] |

ZDR is set at the organization or project level. There is no per-request ZDR flag.

## Endpoints

ZDR-eligible, some with the limitations listed below: [1]

`/v1/chat/completions`, `/v1/responses`, `/v1/decisions`, `/v1/completions`, `/v1/embeddings`, `/v1/moderations`, `/v1/images/generations`, `/v1/images/edits`, `/v1/audio/transcriptions`, `/v1/audio/translations`, `/v1/audio/speech`, `/v1/realtime`, `/v1/live/sessions`

Not ZDR-eligible. These may still store application state even when ZDR is on: [1]

`/v1/conversations`, `/v1/chatkit/threads`, `/v1/agents`, `/v1/assistants`, `/v1/threads`, `/v1/vector_stores`, `/v1/files`, `/v1/fine_tuning/jobs`, `/v1/evals`, `/v1/batches`, `/v1/audio/voices`

## What ZDR still keeps

These are the cases that surprise teams in a security review. All come from OpenAI's data controls page. [1]

- Background mode writes response data to disk for roughly 10 minutes so you can poll for the result. ZDR projects can use the 2-hour and 8-hour background windows, with `store` treated as `false`.
- Prompt caching can keep encrypted key/value tensors on the GPU machines for up to 24 hours. On `gpt-5.5` and `gpt-5.5-pro`, setting `prompt_cache_retention` to `in_memory` returns an error.
- Code Interpreter containers can write temporary state to their filesystem until the container expires or you delete it.
- Images and files are scanned for CSAM on upload. Flagged images are kept for manual review even under ZDR.
- Data sent to a remote MCP server falls under that server's retention policy.
- Live web search is not HIPAA eligible and is not covered by a BAA. Offline mode (`external_web_access: false`) can be covered under specific ZDR conditions.

## Data residency

Data residency is a separate agreement through sales. Each project gets one region at creation, and it cannot be changed later. [3]

| Region | Host | Regional processing |
| :--- | :--- | :--- |
| United States | `us.api.openai.com` | Yes |
| Europe (EEA and Switzerland) | `eu.api.openai.com` | Yes |
| United Arab Emirates | `ae.api.openai.com` | Yes, on request |
| Australia, Japan, India | `au.`, `jp.`, `in.api.openai.com` | Selected models only |
| Canada, Singapore, South Korea, United Kingdom | `ca.`, `sg.`, `kr.`, `gb.api.openai.com` | Storage only |

Models released on or after 5 March 2026 cost 10% more on residency endpoints. System data such as account details, billing and usage statistics can still be stored and processed outside the region. [3]

## Encryption with your own keys

Enterprise Key Management (EKM) encrypts application state stored at OpenAI with a key from your AWS KMS, Google Cloud KMS or Azure Key Vault. It does not support the Assistants API or vision fine-tuning; requests to those in an EKM project return an error. [1]

## Using it

Nothing changes in your code once ZDR is on for the project. Setting `store: false` yourself still makes the intent explicit, and keeps the behaviour the same if a request is ever sent with a key from a project without ZDR.

```bash
curl https://api.openai.com/v1/responses \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "<model>",
    "store": false,
    "input": "Hello"
  }'
```

## How to check it

- Go to Settings, Organization, Data controls, Data Retention, and confirm the policy for the organization and for each project that handles sensitive data. Screenshot it for your audit file. [1]
- Make sure no code path calls a non-eligible endpoint from a ZDR project, such as `/v1/files` or `/v1/assistants`.
- Check whether you use background mode, Code Interpreter, MCP tools or image inputs, and record the retention each one still has.

## Sources

1. OpenAI, [Data controls in the OpenAI platform](https://developers.openai.com/api/docs/guides/your-data). Accessed 25 November 2026.
2. OpenAI, [ZDR with Private Safety Processing](https://developers.openai.com/api/docs/guides/private-safety-processing). Accessed 25 November 2026.
3. OpenAI, [Data residency](https://developers.openai.com/api/docs/guides/data-residency). Accessed 25 November 2026.

---

[← All providers](../README.md#-provider-guides) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
