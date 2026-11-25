# Google Vertex AI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#google-vertex-ai)

Vertex AI does not train on your data, but three things keep data by default: a 24-hour cache, prompt logging for abuse monitoring, and some grounding and agent features. Getting to zero retention means turning off the cache, being exempt from abuse logging, and avoiding the features that cannot be turned off. Google now documents parts of this under the name Gemini Enterprise Agent Platform.

This page is about Vertex AI. The Gemini API in Google AI Studio has different terms.

## Default retention

- Google does not use your data to train or fine-tune any model without your permission. This covers every managed model, including pre-GA ones. [1]
- Gemini models cache data in memory and on disk by default. The cache is deleted no later than 24 hours after it is written. The disk cache uses Google's default encryption; CMEK is not supported for it. [1]
- For standard models, Google may log prompts flagged by automated classifiers to check for Acceptable Use Policy violations. Those logs are kept for up to 90 days in the same region and are not encrypted with CMEK. [2]

## Getting to zero retention

1. Disable caching for the project. This needs `roles/aiplatform.admin`. To disable it for a whole organization, ask your account team. [1]

   ```bash
   curl -X PATCH \
     -H "Authorization: Bearer $(gcloud auth application-default print-access-token)" \
     -H "Content-Type: application/json" \
     "https://us-central1-aiplatform.googleapis.com/v1/projects/${PROJECT_ID}/cacheConfig" \
     -d '{"name": "projects/'"${PROJECT_ID}"'/cacheConfig", "disableCache": true}'
   ```

   Setting `retentionType` to `EPHEMERAL` instead keeps the in-memory cache and turns off only the disk cache. [1]

2. Get out of abuse-monitoring prompt logging. Customers with a Google Cloud Master Agreement are exempt by default for standard models. Others should ask their account team. [2]

3. Avoid or replace the features in the next section that store data and cannot be switched off.

## Features that still store data

From Google's ZDR page [1] unless noted:

| Feature | Retention | Can you turn it off? |
| :--- | :--- | :--- |
| Grounding with Google Search | Query logs up to 3 days | No. Google suggests Web Grounding for Enterprise if you need zero retention. |
| Grounding with Google Maps | Prompts, context and output for 30 days | No |
| Live API session resumption | Up to 24 hours | Off by default; you enable it per request. |
| Interactions API | Stored by default (`store` is `true`) | Yes, set `store` to `false`. |
| Deep Research agent | 7 days | No |
| CodeMender | Up to 7 days | You can delete a session earlier. |
| Request-response logging | Your BigQuery table | Off by default. |

## Frontier and partner models

The rules are different for Google's frontier models and for "Advanced AI" models from other providers. [2]

- Google Frontier Models (the Gemini 4 Carbon and Argon Cyber variants) may log prompts and responses for up to 30 days inside your environment. Master Agreement customers get ZDR-aligned logging and no human review.
- Non-Google Advanced AI models log all prompts and responses for up to 30 days. This includes every Claude Mythos and Claude Fable version, and newer Claude Opus and Sonnet models when `advanced_ai_enabled` is set for Anthropic's Cyber Verification Program. You consent once per project to the Advanced AI Safety Addendum in Model Garden. Only approved ZDR projects are exempt.
- For Claude Fable 5 and Mythos 5 you must enable sharing data with Anthropic for abuse monitoring. Claude on Vertex AI is governed by Anthropic's Commercial Terms. [2] See also [Anthropic](anthropic.md).
- OpenAI flagship models are logged for up to 30 days by default. ZDR is available per approved project. [1]

## Private networking

Use VPC Service Controls to stop data leaving a perimeter, and Private Google Access or Private Service Connect so traffic stays off the public internet. [3]

```bash
gcloud access-context-manager perimeters create vertex-perimeter \
  --title="Vertex AI perimeter" \
  --resources="projects/<project-number>" \
  --restricted-services="aiplatform.googleapis.com" \
  --policy=<policy-id>
```

## How to check it

- Read the project's cache configuration back with a `GET` on the same `cacheConfig` URL and confirm `disableCache` is `true`. The `GET` needs `roles/aiplatform.viewer` or higher. [1]
- Confirm in your contract whether you have a Google Cloud Master Agreement, or get the abuse-logging exemption in writing.
- Search your code for Google Search or Maps grounding, Live API session resumption, Deep Research and the Interactions API.
- For Advanced AI models, record which projects have consented to the Safety Addendum and which are approved for ZDR.

## Sources

1. Google Cloud, [Vertex AI and zero data retention](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/vertex-ai-zero-data-retention). Accessed 25 November 2026.
2. Google Cloud, [Abuse monitoring](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/learn/abuse-monitoring). Accessed 25 November 2026.
3. Google Cloud, [VPC Service Controls](https://docs.cloud.google.com/gemini-enterprise-agent-platform/machine-learning/general/vpc-service-controls). Accessed 25 November 2026.

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
