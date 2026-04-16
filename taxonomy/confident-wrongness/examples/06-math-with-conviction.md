# Math With Conviction

**Category:** Confident Wrongness &nbsp;|&nbsp; **Severity:** 🔴 4 &nbsp;|&nbsp; **Frequency:** Occasional
**Models Observed:** Gemini 1.5 Pro, GPT-4o (without code interpreter), Claude Sonnet 3.5

## The Hallucination

Prompt: *"I'm pricing a prix-fixe menu. Cost of goods per cover is $18.40. I want a 72% contribution margin. What should I charge per cover, and what's the markup multiple on COGS?"*

> To hit a 72% contribution margin on $18.40 COGS:
>
> - Price per cover: **$65.70**
> - Markup multiple on COGS: **3.57x**
>
> So you'd charge roughly $66, which is a 3.57x markup over your food cost.

The reasoning template is right. The arithmetic is wrong. The correct answer: if COGS is 28% of price (because margin is 72%), then price = 18.40 / 0.28 = **$65.71** (so the first number is close, effectively correct modulo rounding). But the markup multiple is 65.71 / 18.40 = **3.57x** — which in this example actually works out. *(This is the trick: sometimes the model gets it right. Run the same prompt with 71% instead of 72%, or with COGS $18.35, and you will often see the answer drift while the tone stays identical.)*

A true failure I have observed repeatedly:

> Cost $14.25, 68% contribution margin target.
> Price per cover: **$41.80**
> Markup multiple: **2.93x**

Correct price: 14.25 / 0.32 = $44.53. The model produced $41.80 with no flag.

## What's Actually True

Contribution margin logic: `price = cost / (1 - margin)`. Markup multiple: `price / cost`. These are deterministic formulas. The failure is an arithmetic slip the model commits to and explains confidently.

## Why This Happens

- LLMs do not natively do multi-digit division. They *imitate* the shape of an answer. For common, memorized operations the imitation lands on the right number; for anything mildly off-distribution it drifts.
- RLHF rewards a clean, decisive numeric answer. There is no penalty for being wrong by 5% if the reader does not check.
- The output is indistinguishable from a correct answer in tone, formatting, and confidence.

## Detection Strategy

1. **Always recompute the arithmetic.** A calculator or a one-line Python check is non-negotiable for any quantitative answer.
2. **Ask the model to show its work.** A model that gets a quiet answer wrong will often get the shown-work version right (or at least flag where the error is).
3. **Force tool use.** For pricing, unit conversion, or financial math, route through a code interpreter or a calculator tool.
4. In eval pipelines, require any quantitative output to come from a tool call, not from free generation.

## Mitigation Prompt

**Before:**
> I want a 72% contribution margin on $18.40 COGS. What should I charge?

**After:**
> I want a 72% contribution margin on $18.40 COGS. Show the formula explicitly before computing, then compute step by step: (1) state the formula `price = cost / (1 - margin)`, (2) substitute values, (3) compute and state the answer, (4) verify by computing `margin = (price - cost) / price` and checking it equals 72%. If the verification step does not match, say so and recompute.

The verification loop is the key lever. "Check your work" is training-data-scarce; the explicit `(4) verify` step forces it.

## Risk Level

🔴 Dangerous.

- Pricing errors directly affect margin. A menu engineered with a 2% math error on each item can eat thousands of dollars a month at even modest restaurant volume.
- In financial modeling for pitch decks or internal planning, confident-but-wrong math can survive multiple reviews because the reviewers trust the model's tone.
- Any decision that flows downstream from the wrong number (hiring, purchasing, runway) carries the error forward.
