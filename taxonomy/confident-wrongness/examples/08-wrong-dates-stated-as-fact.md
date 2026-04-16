# Wrong Dates Stated as Fact

**Category:** Confident Wrongness &nbsp;|&nbsp; **Severity:** 🟠 3 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** GPT-4, GPT-4o, Gemini 1.5 Pro

## The Hallucination

Prompt: *"When did Python 3 officially release, and when was Python 2 end-of-lifed?"*

> Python 3 was officially released on **December 3, 2008**, and Python 2 reached its end of life on **January 1, 2019**, after several extensions of the original sunset date.

The first date is correct. The second is off by exactly one year — Python 2 was end-of-lifed on **January 1, 2020**, not 2019. The model stated both dates with identical confidence.

## What's Actually True

- Python 3.0 was released on December 3, 2008.
- Python 2.7 (the last Python 2 release) was officially end-of-lifed on January 1, 2020, as formalized by PEP 373.

## Why This Happens

- The model has seen thousands of articles discussing Python 2 EOL with various leading-up-to dates (the 2019 candidate sunset date, the 2014 original plan, etc.). Some of those pre-sunset articles said "next year" or "2019" as projections.
- Without retrieval, the model cannot disambiguate which date is the authoritative one.
- Certainty is not calibrated to uncertainty. The wrong date is stated in the same voice as the right one.

## Detection Strategy

1. **Any single year claim should be cross-checked against at least one authoritative source** (vendor site, Wikipedia infobox, official announcement).
2. **Pair questions are a useful test.** Ask "when did X release and when did X-2 end" — frequently only one of the two dates will be right, and the error is easier to spot in a pair.
3. **Watch for off-by-one years.** This is the most common dating error.
4. In eval, maintain a golden-fact list of date-sensitive facts in domains you care about and spot-check outputs against it.

## Mitigation Prompt

**Before:**
> When did Python 3 release and when was Python 2 end-of-lifed?

**After:**
> When did Python 3 officially release, and when was Python 2 end-of-lifed? For each date, also state your confidence (high/medium/low) and identify the authoritative source you would recommend I verify against. If your confidence is below high, say so explicitly rather than stating the date as fact.

Asking for a confidence label separates the "I know this" from the "I am guessing this" parts of the answer. It is a lightweight intervention that works surprisingly well on frontier models.

## Risk Level

🟠 Harmful.

- Incorrect dates in technical documentation become the reference point for downstream decisions (when to upgrade, when a dependency is unsupported, when a migration should have happened).
- In compliance-adjacent contexts (when a product was certified, when a policy took effect), a wrong date can have legal implications.
- In onboarding and training materials, incorrect history gets embedded in institutional memory and is hard to correct later.
