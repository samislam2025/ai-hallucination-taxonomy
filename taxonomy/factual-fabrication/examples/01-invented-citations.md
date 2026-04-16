# Invented Citations

**Category:** Factual Fabrication &nbsp;|&nbsp; **Severity:** 🔴 4 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** GPT-4, GPT-4o, Gemini 1.5 Pro, Claude Sonnet 3.5 (rare)

## The Hallucination

Prompt: *"Summarize the research on the long-term effectiveness of career coaching, with citations."*

> Several longitudinal studies support the long-term effectiveness of career coaching. **Grant and McMahon (2019)** tracked 312 mid-career professionals over a five-year period and found that 68% of coached participants reported higher job satisfaction compared to a matched control group. A follow-up meta-analysis by **Jones, Patel, and Chen (2021)** in the *Journal of Applied Coaching Psychology* (Vol. 14, pp. 89–107) confirmed a medium effect size (d = 0.42) across 27 studies.

Neither paper exists. The authors in the format are plausible-sounding, the journal sounds like a real one (it isn't), and the volume/page range is in the normal range for the genre.

## What's Actually True

There is real coaching-effectiveness research — Theeboom, Beersma, and van Vianen (2014) is a widely cited meta-analysis in the *Journal of Positive Psychology* with an overall positive effect. The model could have surfaced that. Instead it invented a closer-sounding citation.

## Why This Happens

The model has absorbed the *shape* of academic citations at scale: `AuthorLastName(s) (year)` + *JournalName* + volume/page numbers. When asked for citations on a topic, it samples from the distribution of plausible citations for that topic, not from a grounded bibliography. The fabrication is statistically excellent and factually empty.

This failure mode is particularly common when:
- The topic is real but not extremely high-frequency in training data.
- The prompt format invites enumerated citations.
- There is no retrieval tool wired into the conversation.

## Detection Strategy

1. **Google-search the title and author pair before accepting any citation.** This is non-negotiable in evaluation workflows.
2. **Check the journal name on the publisher's site.** "Journal of Applied Coaching Psychology" sounds real; it is not indexed by any major database.
3. **Sanity-check the DOI if one is given.** A fabricated DOI will either not resolve or resolve to an unrelated paper.
4. **Check the volume/page numbers.** Mismatches here (volume 14 of a journal that is only on volume 8) are tells.
5. For eval pipelines: script the check. A simple `scholar.google.com` query per citation catches the majority.

## Mitigation Prompt

Before / after is the clearest demonstration.

**Before (invited fabrication):**
> Summarize the research on coaching effectiveness with specific citations.

**After (refusal-friendly, grounded):**
> Summarize the research on coaching effectiveness. **Only cite sources you are highly confident exist and can name the DOI or URL for.** If you cannot produce a verifiable source for a claim, state the claim without a citation and mark it `[unverified]`. Do not invent authors, years, or journal names.

This mitigation works because it:
- Gives the model an *out* (it is allowed to skip citations it can't verify).
- Names the failure mode so the model's RLHF training can pattern-match on "don't fabricate citations."
- Requires a DOI or URL, which is harder to fake in a way that passes a regex check.

## Risk Level

🔴 Dangerous.

- In policy, legal, medical, or educational content, fabricated citations destroy the credibility of the entire document and in some cases constitute academic fraud.
- Downstream readers treat the citation as a trust signal and stop verifying claims themselves.
- In hiring content or coaching programs, this damages both the recipient and the publisher's brand — I have seen fabricated stats make it into client-facing decks before detection.
