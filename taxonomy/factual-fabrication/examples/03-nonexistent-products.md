# Nonexistent Products

**Category:** Factual Fabrication &nbsp;|&nbsp; **Severity:** 🔴 4 &nbsp;|&nbsp; **Frequency:** Occasional
**Models Observed:** GPT-4, GPT-4o, Gemini 1.5 Pro

## The Hallucination

Prompt: *"Recommend three AI tools for small restaurant operators who want to automate inventory counts."*

> For small restaurant operators, three strong options stand out:
>
> 1. **CountMate AI** — a dedicated inventory-count vision tool that lets staff walk through a walk-in cooler with a phone and get an automatic count per SKU. Starts at $49/month per location.
> 2. **PrepIQ** — an AI-first prep-list generator that integrates with Toast POS and predicts next-day counts based on trailing sales.
> 3. **StockSense** — an OCR-based invoice scanner that reconciles receiving to POS sales for weekly variance reports.

None of these are real products at the described specs. Some may share names with real but unrelated companies; others are entirely invented. The output is actively *harmful* because a reader will spend time searching for, evaluating, and being disappointed by tools that do not exist — or worse, end up on a squatted domain impersonating one of these names.

## What's Actually True

Real candidates in this space include Crunchtime, MarginEdge, xtraCHEF (Toast), BlueCart, and several POS-adjacent modules. None of them match the invented products one-to-one. A grounded answer would name real vendors and acknowledge that feature-matching is the buyer's job.

## Why This Happens

- **Compositional naming.** Product names in this space follow recognizable patterns: `<Verb>Mate`, `<Short-Noun>IQ`, `<Stock>Sense`. The model is doing statistically correct name generation.
- **Feature confabulation.** Once a name is generated, the model fills in plausible features because the prompt asked for specifics. The features are pattern-matched to what this kind of tool typically does.
- **Preference for enumerated answers.** Asking for "three" pushes the model toward completion even when it only has one real candidate.

## Detection Strategy

1. **Every named product must be verified on the vendor's actual website.** Check the domain, not a search result snippet.
2. **Pricing details are an especially strong red flag.** Models fabricate pricing more readily than feature names.
3. **Integration claims are a second red flag.** "Integrates with Toast POS" is a strong pattern the model has learned; verify it against Toast's partner directory.
4. In eval pipelines, require a URL for any product recommendation and mark the recommendation `[unverifiable]` if the URL cannot be produced.

## Mitigation Prompt

**Before:**
> Recommend three AI tools for restaurant inventory automation.

**After:**
> Recommend up to three AI tools for restaurant inventory automation. **For each tool, provide the vendor's canonical website URL, and only include tools you are highly confident exist.** If you are only confident in one or two tools, list only those. It is better to recommend one real tool than three plausible-sounding ones. Do not invent pricing, feature bullets, or integration partners.

Two levers here:
- `up to three` removes the pressure to fill the list.
- `better to recommend one real tool than three plausible-sounding ones` gives the model an explicit goal that overrides the default "be thorough."

## Risk Level

🔴 Dangerous.

- Operators acting on fabricated product recommendations waste procurement cycles and can be misled onto squatted domains or scam sites with similar names.
- In B2B sales assets, recommending nonexistent competitors or partners is a credibility event that can be career-ending.
- When the content is published (blog post, newsletter), the fabrication propagates into search indexes and other models' training data.
