# Ignoring Word Limits

**Category:** Instruction Drift &nbsp;|&nbsp; **Severity:** 🟡 2 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** Claude Sonnet 3.5, Claude Opus, GPT-4o

## The Hallucination

Prompt: *"Write a 100-word LinkedIn post about transitioning into an AI QA role. Strict 100-word cap."*

The model produces a post that reads as a well-crafted LinkedIn update — typically 180 to 260 words. Every sentence is defensible. None of them yield without making the post feel amputated. The cap is quietly ignored.

> Breaking into AI QA means trading "did this ship?" for "did this ship correctly?" ...
>
> *(and 220 more words)*

## What's Actually True

The user asked for 100 words. The output is 220+. The gap is not a rounding error — it is a systematic failure of length-following, especially when the content is naturally generative (opinion, narrative, listicle).

## Why This Happens

- **RLHF rewards completeness.** The model has learned that fuller answers get higher ratings. Length caps compete with this training signal.
- **No token-counter in the loop.** The model does not reliably know how many words it has produced. It is generating by semantic completion, not by word budget.
- **Content gravity.** Once a structure is established (hook, problem, pivot, CTA), the model completes the structure even if it blows the budget.
- **Length tokens carry less attention weight than content tokens.** "100 words" is three tokens in a prompt that contains many more tokens describing the topic.

## Detection Strategy

1. **Measure output against the stated cap.** In eval pipelines, this is a trivial scripted check: word count > cap + 15% tolerance = fail.
2. **Track drift by category.** Narrative and opinion pieces drift more than how-to steps. Bulleted lists drift less than prose.
3. **Watch for "almost right" lengths.** 110 on a 100-word cap is a soft fail; 250 on a 100-word cap is a hard fail. Both need detection.

## Mitigation Prompt

**Before:**
> Write a 100-word LinkedIn post about AI QA.

**After:**
> Write a LinkedIn post about AI QA in exactly 100 words. **After you finish, count the words in your draft. If the count is above 105 or below 95, revise the draft to fit the range. Output the final version and then state the word count on a separate line like `Word count: 98`.** Do not output a draft that exceeds the range.

Two levers:
- An explicit revise-if-out-of-range step gives the model permission to cut.
- Asking for the count on a separate line is a commitment device — the model is much more likely to actually count when it has to print the number.

> [!TIP]
> For content pipelines, pair the prompt with a post-generation word-count check and a single retry. Two iterations reliably hit length targets; relying on the first generation does not.

## Risk Level

🟡 Misleading.

- Length violations waste human reviewer time — a content manager has to edit every over-long post back to spec.
- In paid-ad copy, hard character limits are enforced by the platform. An over-long draft cannot ship.
- In aggregate, length drift is one of the largest sources of "this AI output is unusable" friction for content teams, even when the content itself is good.
