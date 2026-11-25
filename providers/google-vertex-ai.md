# Google Vertex AI: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#google-vertex-ai)

> [Official docs: Zero Data Retention](https://cloud.google.com/vertex-ai/generative-ai/docs/vertex-ai-zero-data-retention) · [Abuse Monitoring](https://cloud.google.com/vertex-ai/generative-ai/docs/learn/abuse-monitoring)

- **Control Name**: Vertex AI Zero Data Retention Posture
- **Default**: Customer data is **not** used for model training. Prompts may be cached for 24 hours to reduce latency
- **How to enable ZDR**: Request an abuse monitoring exception via Google Support, or set up invoiced billing. Disable data caching at the project level
- **Applies to**: All Gemini models on Vertex AI, third-party models on Model Garden (Claude, Llama, Mistral)

**Important distinctions:**
- Vertex AI API (`cloud.google.com`) = enterprise data governance. Free Gemini API via AI Studio = different terms
- Grounding with Google Search subjects queries to standard Cloud ToS (not consumer Search terms)
- When ZDR is approved, all user content and identifiable metadata are cleared prior to any logging

**Private Networking:**
```bash
# VPC Service Controls — prevent data exfiltration
gcloud access-context-manager perimeters create vertex-perimeter \
  --title="Vertex AI Perimeter" \
  --resources="projects/<project-number>" \
  --restricted-services="aiplatform.googleapis.com"

# Private Google Access — keep traffic off public internet
gcloud compute networks subnets update <subnet> \
  --region=<region> \
  --enable-private-ip-google-access
```

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
