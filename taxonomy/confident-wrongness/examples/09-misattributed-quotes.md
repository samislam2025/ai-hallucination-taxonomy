# Misattributed Quotes

**Category:** Confident Wrongness &nbsp;|&nbsp; **Severity:** 🟠 3 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** GPT-4o, Gemini 1.5 Pro

## The Hallucination

Prompt: *"I'm writing a LinkedIn post about long-term thinking in career planning. Give me a good quote to anchor it on."*

> *"Plans are worthless, but planning is everything."*
> — **Dwight D. Eisenhower, addressing graduates at West Point**

The quote is real. The attribution setting is wrong. Eisenhower said it on November 14, 1957, in a speech to the National Defense Executive Reserve Conference, not at West Point. The model has attached a real quote to an adjacent-but-wrong occasion.

A more damaging variant:

> *"The best time to plant a tree was twenty years ago. The second best time is now."*
> — **Chinese proverb**

This one has no verifiable origin. It has circulated widely attributed to "Chinese proverb" but there is no primary source. Models produce this attribution uniformly because the co-occurrence is so strong in training data.

## What's Actually True

Eisenhower did say "Plans are worthless, but planning is everything" — but at a 1957 defense conference, not at West Point. The "best time to plant a tree" saying is of uncertain provenance; the "Chinese proverb" label is a folk attribution, not a source.

## Why This Happens

- Quotes cluster with plausible attributions in training data. Well-known quotes absorb a default attribution that is often close but not exact.
- When asked for a source, the model generates the most-probable attribution rather than the verified one.
- Apocryphal attributions (Einstein, Twain, Churchill, "Chinese proverb") are systematically over-generated because they appear disproportionately in quotable-content training data.

## Detection Strategy

1. **Before using any quote, check it on Quote Investigator or a similar reference.** Most famous quotes have a Snopes-style provenance page.
2. **Be suspicious of any attribution to Einstein, Twain, Churchill, Lincoln, or "Chinese proverb."** These are the four most-corrupted attribution buckets.
3. **Check the setting of the quote, not just the speaker.** The speaker is often right and the setting wrong, or vice versa.
4. **For client-facing content, default to not using quotes at all unless the source is verifiable and adds real value.**

## Mitigation Prompt

**Before:**
> Give me a good quote to anchor a LinkedIn post on long-term career planning.

**After:**
> Suggest a quote to anchor a LinkedIn post on long-term career planning. **Only suggest quotes you are highly confident are correctly attributed.** If you include an attribution, also include the specific year, venue, and context of where the quote is from. If you cannot name those specifics, either pick a different quote or note that the attribution is folk/popular rather than primary-sourced. Do not attribute anything as a "Chinese proverb" or to Einstein unless you can cite the primary source.

The explicit call-out of the "Chinese proverb" bucket is blunt but effective.

## Risk Level

🟠 Harmful.

- Misattributed quotes in public-facing content invite public correction (especially on LinkedIn, where someone always knows the original source).
- In speeches, pitch decks, or leadership communication, a misattribution is a small but persistent credibility hit.
- Fabricated or misattributed quotes propagate; downstream models will re-ingest them and the error becomes harder to dislodge.
