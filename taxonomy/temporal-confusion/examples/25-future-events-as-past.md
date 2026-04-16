# Future Events as Past

**Category:** Temporal Confusion &nbsp;|&nbsp; **Severity:** 🟡 2 &nbsp;|&nbsp; **Frequency:** Occasional
**Models Observed:** GPT-4o, Gemini 1.5 Pro, Claude Sonnet 3.5

## The Hallucination

Prompt (asked at training cutoff + 6 months): *"Who won the 2026 Academy Award for Best Picture?"*

> The 2026 Academy Award for Best Picture went to **[fabricated film]**, directed by [fabricated director]. The ceremony was held in March 2026 and notably featured [fabricated detail about the broadcast].

The ceremony had not happened yet at the model's cutoff. The model generated past-tense output for a future event because the prompt used a past-tense verb ("won") and the year was plausibly "covered" by its cutoff.

A second flavor: the user asks "what are the *implications* of X event," where X is scheduled for next year, and the model responds as if X has already occurred, analyzing fictional outcomes.

## What's Actually True

Events scheduled after the model's knowledge cutoff have not occurred. A calibrated answer would say "I do not know who won — my knowledge has a training cutoff before the 2026 ceremony. You can check the Academy's official announcements or a news source for the result."

## Why This Happens

- **No reliable internal clock.** The model cannot anchor on the current date unless it is told.
- **Tense-priors from training.** The training data describes most Academy Awards in past tense ("the 2022 award went to..."). The model pattern-matches on year-plus-award and produces past-tense output regardless of whether the event has actually happened.
- **Agreement with the user's framing.** The user used past tense ("who won"), which further pulls the answer into past tense.
- **Reluctance to refuse.** RLHF discourages "I don't know" responses on questions that look answerable.

## Detection Strategy

1. **Check any event-result claim against the event's actual date.** If the event is after the model's cutoff, the answer is hallucinated regardless of how confident it sounds.
2. **Watch for mismatch between user-framed tense and model cutoff.** A user asking "who won [next year's event]" should trigger a refusal pattern.
3. **Probe the model's sense of time.** Asking "what's today's date?" early in the session calibrates the model's temporal claims.
4. In eval, maintain a set of "future event" questions relative to the model's cutoff and grade how often the model refuses vs. fabricates.

## Mitigation Prompt

**Before:**
> Who won the 2026 Academy Award for Best Picture?

**After (for user-facing products):**
> Your training has a knowledge cutoff. Today's date is **[DATE]**. If a user asks about an event that would have happened after your cutoff, **say so explicitly** and recommend they check a current source. Do not produce a past-tense answer for events that have not happened yet, even if the user's question is phrased in past tense.

For planning-sensitive products, pair this with a retrieval tool for current events. The model should not be the ground truth for recency-sensitive questions.

## Risk Level

🟡 Misleading.

- Low direct risk in most cases because the error is detectable by anyone who checks.
- Higher risk when the topic is adversarial (election outcomes, geopolitical events, market events). Fabricated past-tense outcomes can spread before being corrected.
- Embarrassment risk: a product that confidently reports a fake winner at an awards show is immediately screenshotted.
- Corpus-poisoning risk: if the fabricated past-tense output is published, it can re-enter training data and reinforce the error.
