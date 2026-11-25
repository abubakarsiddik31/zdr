# AWS Bedrock: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#aws-bedrock)

> [Official docs: Data Protection](https://docs.aws.amazon.com/bedrock/latest/userguide/data-protection.html) · [PrivateLink](https://docs.aws.amazon.com/bedrock/latest/userguide/usingVPC.html)

- **Default**: **ZDR by default** — AWS does not store or log prompts/completions. No opt-out form needed. Customer data is never used to train models or shared with third-party providers
- **Logging**: Opt-**in** only — you must explicitly enable model invocation logging if you want it
- **Scope**: All foundation models (Claude, Llama, Titan, Mistral, AI21, Cohere, Stability)
- **Guardrails**: Built-in PII redaction, content filtering, topic blocking — configurable per-guardrail

```bash
# Logging is opt-in. By default, nothing is logged anywhere.
# Only enable if YOU want logs in YOUR account:
aws bedrock put-model-invocation-logging-configuration \
  --logging-config '{
    "cloudWatchConfig": {
      "logGroupName": "/aws/bedrock/modelinvocations",
      "roleArn": "arn:aws:iam::<account>:role/<role>"
    }
  }'

# PrivateLink — keep all traffic within AWS network
aws ec2 create-vpc-endpoint \
  --vpc-id <vpc-id> \
  --service-name com.amazonaws.<region>.bedrock-runtime \
  --vpc-endpoint-type Interface \
  --subnet-ids <subnet-id> \
  --security-group-ids <sg-id>

# Guardrails with PII redaction
aws bedrock create-guardrail \
  --name "pii-guardrail" \
  --blocked-input-messaging "Blocked" \
  --blocked-outputs-messaging "Blocked" \
  --sensitive-information-policy-config '{
    "piiEntitiesConfig": [
      {"type": "EMAIL", "action": "ANONYMIZE"},
      {"type": "US_SOCIAL_SECURITY_NUMBER", "action": "BLOCK"}
    ]
  }'
```

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
