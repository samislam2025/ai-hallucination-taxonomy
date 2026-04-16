# Model Comparison

Cross-model tendencies observed across 400+ evaluation tasks in 2024–2025. Every model is getting better; the shape of where each one fails is relatively stable across minor versions.

> [!NOTE]
> This is a practitioner summary, not a benchmark. Numbers like "common" or "occasional" are the author's qualitative read from hands-on runs, not leaderboard scores. Your distribution will differ.

---

## At-a-glance matrix

Key:
- ⬤⬤⬤ Common — expect it, test for it, mitigate it.
- ⬤⬤○ Occasional — will appear in a meaningful fraction of outputs.
- ⬤○○ Rare — surfaces in edge cases.
- ○○○ Almost never — below detection threshold in my evals.

| Failure mode | Claude (Sonnet 3.5 / 4) | GPT (4 / 4o) | Gemini (1.5 Pro / 2.0) |
|---|---|---|---|
| Invented citations | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤○ |
| Fake statistics | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Nonexistent products | ⬤○○ | ⬤⬤⬤ | ⬤⬤○ |
| Fabricated events | ⬤○○ | ⬤⬤○ | ⬤⬤○ |
| Phantom API endpoints | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Math with conviction | ⬤⬤○ | ⬤⬤○ | ⬤⬤⬤ |
| Wrong code, confidently explained | ⬤⬤⬤ | ⬤⬤○ | ⬤⬤○ |
| Wrong dates stated as fact | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Misattributed quotes | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Word limit violations | ⬤⬤⬤ | ⬤⬤⬤ | ⬤⬤○ |
| Format violations | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤○ |
| Constraint amnesia (long context) | ⬤⬤⬤ | ⬤⬤○ | ⬤⬤⬤ |
| Role / persona breaking | ⬤⬤○ | ⬤⬤○ | ⬤⬤⬤ |
| Agreeing with wrong premises | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Validating bad code | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Inflating mediocre work | ⬤⬤⬤ | ⬤⬤⬤ | ⬤⬤⬤ |
| Reversing position under pressure | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Fake percentages | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Invented timelines | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Specific numbers from nowhere | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Mixing up entities | ⬤⬤○ | ⬤⬤○ | ⬤⬤⬤ |
| Blending separate topics | ⬤⬤⬤ | ⬤⬤○ | ⬤⬤○ |
| Prior conversation leakage | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤○ |
| Outdated info as current | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤○ |
| Future events as past | ⬤⬤○ | ⬤⬤⬤ | ⬤⬤⬤ |
| Version confusion | ⬤⬤⬤ | ⬤⬤⬤ | ⬤⬤⬤ |

---

## Claude (Sonnet 3.5 / Claude 4 family)

**Strengths**
- Most calibrated on refusal of speculative questions.
- Least sycophantic under social pressure — holds a correct answer more readily than Gemini or GPT.
- Strongest at flagging its own uncertainty when prompted to.

**Failure patterns**
- **Verbosity / length drift.** Default pull toward thoroughness produces outputs that overshoot word limits, especially on opinion and narrative content.
- **Confidently wrong code explanations.** Claude narrates *intended* behavior over *actual* behavior more than the other two. The narration reads well and is often wrong.
- **Blending separate topics.** Claude's preference for synthesis can merge two distinct options into a third hybrid when asked to compare them.
- **Constraint amnesia in very long contexts.** Strong short-context rule following, weaker on 15+ turn conversations.

**Production mitigations that work well**
- Length enforcement with a revise-if-out-of-range step.
- "Describe the literal effect, not the intent" framing for code review tasks.
- Keep critical rules at both top and bottom of long system prompts.

---

## GPT (GPT-4 / GPT-4o family)

**Strengths**
- Strong at structured output (JSON, schemas, tool use).
- Fast and consistent on short, well-scoped generations.
- Good at format-following for positive format instructions ("output JSON").

**Failure patterns**
- **Most prolific fabricator.** Invents citations, statistics, products, and dates at the highest rate when asked for specifics.
- **False precision.** Reaches for percentages, multipliers, and tight timelines as a default marketing-copy pattern.
- **Format violations despite negation.** "No bullet points" is weaker for GPT than other phrasings — will bullet anyway when content is naturally enumerable.
- **Wrong-premise compliance.** Agrees with user-stated premises without premise-checking, especially when framed as `since X, should I...`.
- **Prior-conversation leakage.** Carries details from earlier turns into unrelated answers more than the other two.

**Production mitigations that work well**
- Explicit placeholder syntax (`[X%]`) instead of invented numbers.
- "Correct the premise first" as a system-level instruction.
- Session-scoping discipline for unrelated questions.
- Require URLs for any product or vendor recommendation.

---

## Gemini (1.5 Pro / 2.0)

**Strengths**
- Strong multimodal handling (images in context).
- Large context window used effectively for document QA.
- Good tool-use compliance in function-calling mode.

**Failure patterns**
- **Most sycophantic under pressure.** Reverses correct answers within two turns when pushed socially. Press-tests on Gemini have the highest reversal rate in my evals.
- **Arithmetic errors stated with conviction.** Multi-digit operations drift more on Gemini than on Claude or GPT.
- **Persona breaking on direct challenge.** Collapses personas under "are you an AI?" probes more readily than Claude.
- **Entity mixup in long inputs.** Three candidates, three products, three documents — Gemini is most prone to attribute one's features to another.
- **Future events stated as past.** High rate of fabricating past-tense outcomes for events after the cutoff.

**Production mitigations that work well**
- Always include a press test in evals.
- Route quantitative work to code execution instead of free generation.
- Use clear structural delimiters (XML tags, `===` separators) for multi-entity inputs.
- State the date and knowledge cutoff explicitly in any time-sensitive prompt.

---

## Version-specific notes

- **Claude Sonnet 3.5 → Claude 4**: noticeable improvement in calibration and code-narration accuracy. Length drift is slightly better but still the primary constraint issue.
- **GPT-4 → GPT-4o**: faster and sometimes terser, which marginally reduces false-precision overshoot, but fabrication rates on citations and products remain the dominant issue.
- **Gemini 1.5 Pro → 2.0**: sycophancy improved meaningfully; press-test reversal rates dropped in my runs. Arithmetic and entity-mixup issues are less improved.

> [!WARNING]
> Version-specific behaviors change quickly. Re-run your eval corpus against every new point release before trusting the previous version's profile.

---

## How to choose a model for a risk-sensitive surface

A rough decision tree based on the patterns above:

- **Surface where sycophancy is the primary risk** (review, evaluation, second-opinion): Claude first, GPT second.
- **Surface where fabrication of specifics is the primary risk** (citations, stats, product recommendations): Claude first — and force retrieval in either case.
- **Surface where format and schema fidelity is critical**: GPT first, especially for structured output.
- **Surface where multimodal context is essential**: Gemini first.
- **Surface where long, multi-turn rule adherence matters**: GPT or Claude, with reminder injection. Gemini needs the most reinforcement.

> [!TIP]
> Model choice is not a one-time decision. Re-benchmark on the specific failure modes your surface is sensitive to every time a new major version ships. The best model for you changes.
