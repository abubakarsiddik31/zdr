# Verifying and Auditing a ZDR Setup

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#verification--audit-guide)

A credible ZDR audit requires **Four Pillars of Evidence**:

```mermaid
flowchart LR
    subgraph P1["1. Configuration"]
        C1["Dashboard screenshots"]
        C2["CLI output\n(ContentLogging: false)"]
        C3["API responses\nconfirming ZDR active"]
    end

    subgraph P2["2. Negative Tests"]
        N1["Attempt data retrieval\n→ expect 404"]
        N2["Check provider logs\n→ expect empty"]
        N3["Query abuse monitor\n→ expect no records"]
    end

    subgraph P3["3. Environment Audit"]
        E1["App logs"]
        E2["Gateway logs"]
        E3["Error tracking"]
        E4["DB query logs"]
    end

    subgraph P4["4. Contracts"]
        K1["Signed BAA"]
        K2["Signed DPA"]
        K3["ZDR Addendum"]
        K4["SOC 2 Report"]
    end

    P1 --> Audit(["ZDR Audit\nComplete ✓"])
    P2 --> Audit
    P3 --> Audit
    P4 --> Audit

    style P1 fill:#e3f2fd,stroke:#3498db
    style P2 fill:#fff3e0,stroke:#f39c12
    style P3 fill:#fce4ec,stroke:#e74c3c
    style P4 fill:#e8f5e9,stroke:#2ecc71
    style Audit fill:#2ecc71,stroke:#1a9c54,color:#fff
```

## 1. Configuration Artifacts

Capture proof that ZDR is enabled:

```bash
# Azure OpenAI — verify ContentLogging is disabled
az cognitiveservices account show --name <resource> --resource-group <rg> \
  --query "properties.capabilities[?name=='ContentLogging'].value"
# Expected: "false"

# AWS Bedrock — verify no logging configured
aws bedrock get-model-invocation-logging-configuration
# Expected: empty or no cloudwatch/s3 config

# OpenAI — screenshot Dashboard > Settings > Organization > Data Retention showing ZDR enabled
```

## 2. Negative Tests

Attempt to retrieve data that shouldn't exist:

```bash
# OpenAI — attempt to retrieve a completion (should fail under ZDR)
curl https://api.openai.com/v1/chat/completions/<completion-id> \
  -H "Authorization: Bearer $OPENAI_API_KEY"
# Expected: 404 or error

# AWS Bedrock — check CloudWatch for model invocation logs
aws logs filter-log-events \
  --log-group-name "/aws/bedrock/modelinvocations" \
  --start-time $(date -d '1 hour ago' +%s000)
# Expected: empty or log group doesn't exist
```

## 3. Environment Audit

Ensure YOUR infrastructure isn't logging what you're trying to protect:

- [ ] Web framework request body logging — disabled or post-redaction only
- [ ] HTTP client libraries — set to WARN+ log level in production
- [ ] API gateway / load balancer — configured to not log request bodies
- [ ] Error tracking (Sentry, Datadog) — `before_send` hooks strip sensitive fields
- [ ] LLM observability tools (LangSmith, Langfuse) — PII redaction enabled
- [ ] Database query logging — parameterized queries, no full statement logging
- [ ] WAF / DLP proxy — not storing payloads in its own logs

## 4. Contractual Proof

Collect signed agreements:
- [ ] BAA (Business Associate Agreement) — for HIPAA
- [ ] DPA (Data Processing Agreement/Addendum) — for GDPR
- [ ] ZDR Addendum or Amendment — provider-specific
- [ ] SOC 2 Type II report — from the provider's trust center

---

[← Back to the guide](../README.md) · Spotted a change in this page? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
