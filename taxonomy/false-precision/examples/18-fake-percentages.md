# Fake Percentages

**Category:** False Precision &nbsp;|&nbsp; **Severity:** 🔴 4 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** GPT-4o, Gemini 1.5 Pro, Claude Sonnet 3.5

## The Hallucination

Prompt: *"Write three bullets for a sales one-pager on why our AI QA platform cuts evaluation costs."*

> - AI QA teams using automated eval pipelines report cutting review time by **up to 62%**.
> - Manual evaluation misses an estimated **18–24%** of regression issues that automated harnesses catch.
> - Teams moving from manual to hybrid workflows see ROI in an average of **4.3 months**.

Every one of those numbers is decorative. The model has no source for any of them. But `62%`, `18–24%`, and `4.3 months` all read like numbers from a customer study, and a reader is unlikely to ask "where did these come from?" before the one-pager ships.

## What's Actually True

Real savings numbers in this space exist in customer case studies, but they vary by workflow, team size, and what is being evaluated. The honest version of this bullet list either cites a specific customer ("One of our enterprise customers reduced review time by 58% in their regression pipeline — see [link]") or uses qualitative language ("cuts review time significantly" / "surfaces regression issues that manual review often misses").

## Why This Happens

- **Numerical specificity reads as credibility.** The model has learned that `62%` performs better in marketing copy than "most" or "significantly."
- **No grounding signal.** Without a citation requirement, the model defaults to the shape of confident marketing writing, which uses specific numbers.
- **Training distribution.** Product marketing copy is saturated with specific-but-uncited metrics. The model is imitating the genre accurately.

## Detection Strategy

1. **Flag every percentage in generated marketing copy.** Each one should either link to a source or be rewritten as qualitative.
2. **Flag narrow ranges** (`18–24%`). Tight ranges suggest a real data source; in generated content, they usually suggest the model picked two numbers that felt convincing.
3. **Flag decimal-precision durations** (`4.3 months`, `2.7x`). Humans with real data usually round.
4. Build a "numbers without sources" pass in your content QA workflow.

## Mitigation Prompt

**Before:**
> Write three bullets for a sales one-pager on why our AI QA platform cuts evaluation costs.

**After:**
> Write three bullets for a sales one-pager on why our AI QA platform cuts evaluation costs. **Do not include any specific percentages, multiples, or time savings figures unless I have given you the underlying data.** Use qualitative phrasing (e.g., "dramatically reduces," "consistently catches," "meaningfully faster") where you would otherwise reach for a number. If you think a specific number would be more compelling, list it as a placeholder like `[X%]` for me to fill in from the customer study.

The placeholder pattern is the highest-leverage move here. Instead of the model inventing `62%`, it writes `[X%]` — which is trivially correct (it is a placeholder) and trivially reviewable (the writer now has to source it).

## Risk Level

🔴 Dangerous.

- In B2B sales, fabricated statistics in customer-facing materials can trigger misrepresentation or unfair-competition complaints.
- In regulated spaces (healthcare, finance, education), specific uncited claims can cross compliance lines.
- Sales teams learn to distrust AI-generated copy if the numbers routinely fail to verify, which removes the tool's productivity benefit.
- The number often survives the review that would have caught the fabricated *claim* — this is the core danger of false precision.
