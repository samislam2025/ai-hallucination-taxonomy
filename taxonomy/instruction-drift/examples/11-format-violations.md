# Format Violations

**Category:** Instruction Drift &nbsp;|&nbsp; **Severity:** 🟡 2 &nbsp;|&nbsp; **Frequency:** Very Common
**Models Observed:** GPT-4o, Claude Sonnet 3.5, Gemini 1.5 Pro

## The Hallucination

Prompt: *"Write this as prose. No bullet points, no headers, no numbered lists. Just flowing paragraphs."*

The model produces:

> Here is a breakdown of how to think about this:
>
> - Start by clarifying your audience.
> - Decide on the format they expect.
> - Build a draft that honors both.
>
> From there, iterate.

Three bullets where there were supposed to be none. The model cannot help itself; the content is naturally enumerable, and the bullet prior is strong.

## What's Actually True

The user asked for prose. They wanted:

> Start by clarifying your audience, then decide on the format they expect, and build a draft that honors both. From there, iterate.

Same content, compliant format.

## Why This Happens

- **Bullet prior is huge in training data.** How-to content on the internet is overwhelmingly bulleted. The model's first instinct for any enumerable content is to bullet it.
- **Structure pre-commit.** The model often internally "decides" on structure early in generation. By the time the `-` tokens appear, reversing is costly.
- **Weak negation.** Instructions phrased as negations ("no bullet points") are less effective than instructions phrased as positive directives ("write as continuous prose").
- **Long system prompts dilute format rules.** In production prompts with persona + rules + examples, a single "no bullets" line gets attention-crowded by other tokens.

## Detection Strategy

1. **Regex the output for disallowed structures.** In eval, a simple `^[-*•]` check over lines catches most bullet violations.
2. **Header check.** Scan for `^#` lines when headers were disallowed.
3. **Numbered-list check.** `^\d+\.` at the start of a line.
4. **Markdown table check.** `\|.+\|` patterns.
5. Run a format-compliance scorer as a gate in content pipelines.

## Mitigation Prompt

**Before:**
> Write this as prose. No bullet points.

**After:**
> Write this as continuous prose — complete sentences joined into flowing paragraphs, with no markdown formatting of any kind. Specifically: no bullet points, no numbered lists, no headers, no bold, no italics. The output should read like a magazine essay, not a LinkedIn post. If the content feels naturally enumerable, join the items with transition words like "first," "then," and "finally" rather than listing them.

Two tricks:
- Positive framing ("write like a magazine essay") outperforms pure negation.
- Providing an escape path ("join with transition words") tells the model what to do with enumerable content instead of leaving it no option but to bullet.

> [!NOTE]
> On repeat offenders (especially GPT-4o), adding a system-level rule + a formatted example outperforms a long verbal instruction. Examples are stronger than rules for format enforcement.

## Risk Level

🟡 Misleading.

- In client-facing deliverables, format violations break voice and look unprofessional.
- In API consumers that parse expected formats (e.g., a downstream component expecting a JSON object), format drift breaks the pipeline.
- In long-form editorial content, unwanted bullets turn a narrative essay into a laundry list, undermining the voice.
