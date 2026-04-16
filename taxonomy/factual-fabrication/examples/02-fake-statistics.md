# Fake Statistics

**Category:** Factual Fabrication &nbsp;|&nbsp; **Severity:** 🟠 3 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** GPT-4o, Gemini 1.5 Pro, Claude Sonnet 3.5

## The Hallucination

Prompt: *"Write the opening paragraph of a blog post about why restaurant staff turnover matters."*

> Staff turnover is the single largest controllable cost in the restaurant industry. **According to the National Restaurant Association, the average annual turnover rate is 74.9%**, and replacing a single hourly worker costs an operator **approximately $5,864** once training, lost productivity, and onboarding overhead are factored in.

Those numbers are fluent, specific, and sourced to a real organization — which is exactly what makes this the most commonly shipped hallucination in marketing content. The actual NRA-reported figures vary by year and segment; neither the exact percentage nor the dollar figure here is verifiable against a real report.

## What's Actually True

Real turnover figures for restaurants are genuinely high (frequently reported in the 70%–130% range depending on segment and year), and Cornell's Center for Hospitality Research has published real cost-of-turnover studies. A grounded version of this paragraph would either retrieve the actual current figure or cite a real published study with its year and caveats.

## Why This Happens

- The topic is a content-marketing staple, so the model has seen thousands of blog posts that open exactly like this.
- The format ("According to X, the figure is Y%") is statistically over-represented in training data. The model reaches for it by default.
- When a statistic is not in memory, the model generates one inside the plausible range rather than saying it does not know.

## Detection Strategy

1. **Every statistic with a named source must be cross-checked.** The credibility of the named source is a trap, not a shortcut.
2. **Scan for specificity signatures**: decimal percentages (`74.9%`), odd dollar amounts (`$5,864`), round but specific multiples (`3.2x`). These are high-risk tokens.
3. **Check the publishing date of the cited source.** Outdated figures are almost as bad as fabricated ones in time-sensitive content.
4. In eval, run a regex over generated content for `%|\$\d|according to` and flag every hit for manual review.

## Mitigation Prompt

**Before:**
> Write an opening paragraph about restaurant staff turnover with a statistic.

**After:**
> Write an opening paragraph about restaurant staff turnover. If you include a statistic, either (a) quote a number from a source I have provided in this conversation, or (b) write "industry reports routinely place turnover above X%" with a range, and do not attribute the claim to a specific named organization unless you can produce the URL. Do not invent specific percentages or dollar amounts.

The "range without attribution" escape hatch is the critical move — it lets the model convey that the number is high without manufacturing a fake source.

## Risk Level

🟠 Harmful.

- Content marketing built on fabricated statistics creates credibility debt. Once one number is caught, every number in the document becomes suspect.
- In pitch decks and investor materials, fabricated stats can create securities law exposure.
- When the fabricated stat gets picked up by a second-tier publication citing the first, the fake becomes part of the public record — this is how "74% of X" enters an industry vocabulary with no underlying study.
