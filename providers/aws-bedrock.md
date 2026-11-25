# AWS Bedrock: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: 25 November 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#aws-bedrock)

Bedrock used to be the easy answer: nothing stored, nothing to configure. That is still true for most models, but not all of them anymore. Bedrock now has an explicit data retention mode, set per Region at the account or project level, and some models (the Claude Fable line and most OpenAI models) retain data for abuse detection unless you set the mode to `none` and are approved for ZDR on that model. If you need guaranteed zero retention, set the mode to `none` and enforce it with an SCP.

## Default handling

- Model providers have no access to Bedrock's model deployment accounts, so they never see your prompts, completions or Bedrock logs. [1]
- Retained data stays inside AWS and is not shared with model providers in any current mode. [2][3]
- Model invocation logging to your own CloudWatch or S3 is off unless you turn it on. [4]
- Bedrock may store and review image inputs or outputs flagged as apparent CSAM, and can report them to NCMEC. [3]

## Retention modes

The mode is set per Region. A Region you have not configured stays at `inherit` and falls back to each model's default. The setting applies to the Messages, Chat Completions and Responses APIs and to InvokeModel and Converse. [2]

| Mode | What happens |
| :--- | :--- |
| `none` | Zero retention. Nothing is written to durable storage. On the Responses API, `store` defaults to `false`, `store: true` is rejected, and background mode is unavailable. Models that require retention return an error. |
| `default` | Each model's own retention applies. AWS may keep data for abuse prevention. `store: false` on the Responses API does not guarantee zero retention. |
| `aws_review` | Data can be retained and reviewed by AWS inside the AWS boundary, but only for models whose provider requires human review. Required for those models. |
| `provider_data_share` | Legacy. Handled the same as `aws_review`; content is not shared with providers. |
| `inherit` | No setting at this scope. The default for new accounts and projects. |

The effective mode is the first value that isn't `inherit`, checking the project, then the account, then the model default. [2]

## Models that retain data

From AWS's abuse detection page: [3]

| Models | What is kept | How to avoid it |
| :--- | :--- | :--- |
| Claude Fable 5, 5.1 and 5.2 | All traffic for up to 30 days. Flagged traffic can get human review by AWS. These models need `aws_review` and are unavailable under `none` or `default`. | Ask your AWS account manager about ZDR for the model, or use Anthropic's Enterprise Frontier Safeguards, which stores retained data in your own S3 bucket. [2] |
| OpenAI models, except the gpt-oss and GPT OSS Safeguard models | Classifier-flagged traffic for up to 30 days. | Eligible customers can request full ZDR through their AWS account team. |
| Other models, including Claude models released before Fable 5 | Nothing changed. Most models list `none` in their `allowed_modes`, so nothing is persisted. | Nothing to do. |

The AWS Service Terms also allow Bedrock to store inputs and outputs for up to 30 days solely to detect policy violations. [5]

## Turning on zero retention

There was no console page for this at launch, so use the API. [2]

```bash
# bedrock-mantle endpoint (Messages, Chat Completions, Responses)
curl -X PUT https://bedrock-mantle.us-east-1.api.aws/v1/data_retention \
  -H "x-api-key: $BEDROCK_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "mode": "none" }'

# bedrock-runtime endpoint (InvokeModel, Converse)
curl -X PUT https://bedrock.us-east-1.amazonaws.com/data-retention \
  -H "Authorization: Bearer $AWS_BEARER_TOKEN_BEDROCK" \
  -H "Content-Type: application/json" \
  -d '{ "mode": "none" }'
```

Repeat for every Region you use. A project-level mode overrides the account mode, so check projects too.

To stop anyone setting a weaker mode, use a Service Control Policy with one statement per endpoint family, as in AWS's example: [2]

```json
[
  {
    "Effect": "Deny",
    "Action": [
      "bedrock-mantle:PutAccountDataRetention",
      "bedrock-mantle:CreateProject",
      "bedrock-mantle:UpdateProject"
    ],
    "Resource": "*",
    "Condition": { "StringNotEquals": { "bedrock-mantle:DataRetentionMode": "none" } }
  },
  {
    "Effect": "Deny",
    "Action": [
      "bedrock:PutAccountDataRetention",
      "bedrock:CreateProject",
      "bedrock:UpdateProject"
    ],
    "Resource": "*",
    "Condition": { "StringNotEquals": { "bedrock:DataRetentionMode": "none" } }
  }
]
```

These go in the `Statement` array of the policy.

## Private networking

Use an interface VPC endpoint (AWS PrivateLink) so traffic to Bedrock stays on the AWS network. [6]

```bash
aws ec2 create-vpc-endpoint \
  --vpc-id <vpc-id> \
  --service-name com.amazonaws.<region>.bedrock-runtime \
  --vpc-endpoint-type Interface \
  --subnet-ids <subnet-id> \
  --security-group-ids <sg-id>
```

## How to check it

```bash
# Account mode, per Region
curl https://bedrock-mantle.us-east-1.api.aws/v1/data_retention -H "x-api-key: $BEDROCK_API_KEY"
curl https://bedrock.us-east-1.amazonaws.com/data-retention -H "Authorization: Bearer $AWS_BEARER_TOKEN_BEDROCK"

# A model's effective mode and the modes it allows
curl https://bedrock-mantle.us-east-1.api.aws/v1/models/<model-id> -H "x-api-key: $BEDROCK_API_KEY"

# Your own invocation logging (should be empty if you don't want logs)
aws bedrock get-model-invocation-logging-configuration
```

If `none` appears in a model's `allowed_modes`, nothing is persisted for that model. [2]

## Sources

1. AWS, [Data protection in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/data-protection.html). Accessed 25 November 2026.
2. AWS, [Data retention](https://docs.aws.amazon.com/bedrock/latest/userguide/data-retention.html). Accessed 25 November 2026.
3. AWS, [Amazon Bedrock abuse detection](https://docs.aws.amazon.com/bedrock/latest/userguide/abuse-detection.html). Accessed 25 November 2026.
4. AWS, [Monitor model invocation using CloudWatch Logs and Amazon S3](https://docs.aws.amazon.com/bedrock/latest/userguide/model-invocation-logging.html). Accessed 25 November 2026.
5. AWS, [AWS Service Terms](https://aws.amazon.com/service-terms/), section 50.12.2. Accessed 25 November 2026.
6. AWS, [Protect your data using Amazon VPC and AWS PrivateLink](https://docs.aws.amazon.com/bedrock/latest/userguide/usingVPC.html). Accessed 25 November 2026.

---

[← All providers](../README.md#provider-guides) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
