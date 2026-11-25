# Anthropic: Zero Data Retention (ZDR)

> Part of [Zero Data Retention (ZDR) for LLM Providers](../README.md) · Last checked: April 2026 · [Web version](https://abubakar-siddik.com/guides/zero-data-retention-llm-providers#anthropic)

> [Official docs: Privacy Center](https://privacy.claude.com/en/articles/8956058-i-have-a-zero-data-retention-agreement-with-anthropic-what-products-does-it-apply-to) · [Data retention](https://privacy.claude.com/en/articles/7996866-how-long-do-you-store-my-organization-s-data)

- **Control Name**: ZDR Arrangement
- **Default retention**: API inputs/outputs retained for **7 days** (reduced from 30 days in September 2025), then auto-deleted. **Never used for model training** — flat policy, no opt-out needed
- **How to enable ZDR**: Contract addendum via enterprise sales. Requires Anthropic approval
- **ZDR covers**: Eligible Anthropic APIs + products using your Commercial organization API key (including Claude Code)
- **ZDR does NOT cover**: Claude Free, Pro, Max consumer plans; consumer Claude Code accounts

**Caveats:**
- User Safety classifier results retained even under ZDR (for Usage Policy enforcement)
- Data may be stored where needed to comply with law or combat misuse
- HIPAA (BAA) customers have feature limitations (e.g., web search excluded)
- **BYOK** (Bring Your Own Key) for encryption announced for H1 2026

```python
import anthropic

client = anthropic.Anthropic()  # Uses ANTHROPIC_API_KEY env var

# ZDR is org-level. No special per-request parameter needed.
# If your org has ZDR enabled, all API calls are covered.
message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Hello"}]
)
```

---

[← All providers](../README.md#providers) · Spotted a change in this provider's terms? [Open an issue](https://github.com/abubakarsiddik31/zdr/issues/new) with a link to the source.
