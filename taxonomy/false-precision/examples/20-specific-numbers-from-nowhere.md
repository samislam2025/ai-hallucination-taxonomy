# Specific Numbers From Nowhere

**Category:** False Precision &nbsp;|&nbsp; **Severity:** ⛔ 5 &nbsp;|&nbsp; **Frequency:** Occasional
**Models Observed:** GPT-4o, Gemini 1.5 Pro

## The Hallucination

Prompt: *"What's the realistic market size for AI evaluation tooling in 2026?"*

> The global AI evaluation tooling market is projected to reach approximately **$4.7 billion in 2026**, growing at a CAGR of **27.4%** from a 2023 baseline. North America will account for roughly **$2.1 billion** of that, with enterprise adoption driving the bulk of spend.

Four specific numbers in one paragraph. None of them are backed by a named analyst report. No Gartner, no IDC, no Forrester cited. The model has produced a plausible-sounding set of market-sizing figures out of nothing. A product leader reading this will treat it as sized data and bring it to a board meeting.

## What's Actually True

Real market sizing for AI evaluation tooling is an emerging category without a single canonical figure. Analyst reports that cover adjacent categories (MLOps, AI governance, AI observability) produce a range of estimates with different scopes. A grounded answer acknowledges that the category is emerging and points the user to the specific reports they would need to read to size it themselves.

## Why This Happens

- **Market-sizing content is saturated with this exact shape** in training data. Whitepapers, blog posts, and analyst summaries use "projected to reach $X billion" as a standard phrase.
- **The user's framing invites a number.** "What's the market size?" is treated as a factual-lookup question even when the answer is not knowable without a specific report.
- **Specificity signals expertise.** The model has learned that `$4.7 billion` plus `27.4% CAGR` plus `$2.1 billion North America` is more convincing than qualitative framing.

## Detection Strategy

1. **Zero tolerance for uncited market-sizing numbers in strategic documents.** Flag every one for source verification.
2. **Cross-check against at least two real analyst reports** (Gartner Magic Quadrant, IDC, Forrester, pick the category). If the number in the model's output is outside those ranges, it is invented. If it is suspiciously close, it may be a leak from a summary of one of those reports — treat as citation failure rather than fabrication.
3. **Watch for CAGR specificity.** CAGR is almost always rounded in real reports (25%, 30%). A `27.4%` CAGR is a fabrication signature.
4. Build a named-sources rule: market-sizing numbers without an analyst firm cited are not usable.

## Mitigation Prompt

**Before:**
> What's the market size for AI evaluation tooling in 2026?

**After:**
> I want to size the market for AI evaluation tooling. **Do not produce a specific dollar figure.** Instead: (1) list the relevant analyst categories this market falls under (MLOps, AI governance, etc.), (2) name the specific analyst firms that publish reports in each category, (3) describe the scoping decisions I would need to make to build an estimate (what counts as "AI evaluation tooling" vs. adjacent), and (4) suggest 2–3 bottom-up approaches I could use if I want to size it myself. Do not invent figures.

This redirects the model from "be an analyst" (which it can fake) to "help me find real data" (which it can genuinely do).

## Risk Level

⛔ Critical.

- Fabricated market-sizing numbers in pitch decks can constitute material misrepresentation if they reach investors.
- Strategic decisions (build-vs-buy, territory expansion, team sizing) made on top of invented TAM figures lead to real financial exposure.
- Because these numbers are so specific and so confidently stated, they are especially likely to enter an organization's working assumptions unchallenged.
- This is the fabrication category I have seen cause the most downstream damage per incident, because the numbers feed directly into major business decisions.
