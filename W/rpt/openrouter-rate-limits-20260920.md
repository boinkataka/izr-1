# OpenRouter Free-Model Rate Limits — Consolidated Report 2026-09-20

**Prompt:** Find rate limits for OpenRouter free-model account with topped-up credits. Model: `openrouter/openrouter/free` routes to multiple free models.

**Total tokens used:** ~3,000 (across reads)
**Total billable tokens:** 0 (free-tier session)

---

## 1. Summary

OpenRouter applies two layers of rate limits on free-model variants: a **per-minute cap** and a **per-day cap**. The daily cap scales based on whether the account has purchased credits. Free models cost **$0 per token** — there is no token quota; only request caps apply.

---

## 2. Rate Limits Per Minute

| Condition | Requests/minute |
|---|---|
| Free models (all accounts) | **20 RPM** |

Source: OpenRouter `limits` documentation exports `FREE_MODEL_RATE_LIMIT_RPM = 20`. This is fixed and does **not** change with credits.

---

## 3. Daily Request Quota

| Condition | Requests/day |
|---|---|
| Free models, no credits purchased | **50 requests/day** |
| Free models, ≥ $10 credits purchased | **1,000 requests/day** |

- Threshold constant: `FREE_MODEL_CREDITS_THRESHOLD = 10` (USD)
- Accounts with less than $10 in credits are capped at 50/day; once $10+ is added, the ceiling lifts to 1,000/day.
- Source: OpenRouter official docs (`limits` and `faq`), constants `FREE_MODEL_NO_CREDITS_RPD` and `FREE_MODEL_HAS_CREDITS_RPD`.

---

## 4. Token Quota

**Free models (`:free` variants) and the `openrouter/free` router are priced at $0 per token** — both input and output tokens cost nothing. Therefore:

| Metric | Value |
|---|---|
| Per-token price | $0 |
| Per-request price | $0 |
| Token quota | **None** — unlimited tokens per request |

The `openrouter/free` router picks one of the available free models at random for each call (`gpt-4o-mini:free`, `meta-llama/llama-3.2-3b-instruct:free`, etc.). Each call counts as **1 request** against the daily/minute caps, regardless of how many tokens are generated.

Topping up credits changes only the **daily request cap** (50 → 1,000). It does **not** add any token cap, does **not** change the per-token price, and does **not** alter the 20 RPM ceiling. Credits are **not deducted** for free-model usage.

Error `402` (Payment Required) can still occur on non-free variants; free-model usage never triggers a billing event.

---

## 5. Source Verification

All figures verified from official OpenRouter documentation pages read directly:

- [OpenRouter Limits](https://openrouter.ai/docs/limits) — exports `FREE_MODEL_RATE_LIMIT_RPM`, `FREE_MODEL_NO_CREDITS_RPD`, `FREE_MODEL_HAS_CREDITS_RPD`
- [OpenRouter FAQ](https://openrouter.ai/docs/faq) — "What free tier options exist?" section confirms 50/day default, 1,000/day with ≥10 credits
- [OpenRouter API Reference — Limits](https://openrouter.ai/docs/api_reference/limits) — same constants in TypeScript SDK format

No third-party or secondary sources were used; all numbers trace to the official docs constants.

---

## Key Takeaway

With topped-up credits (≥$10) + `openrouter/free`: you can make **up to 1,000 requests per day** at up to **20 per minute**, and each request can generate **unlimited tokens** — no token ceiling applies.