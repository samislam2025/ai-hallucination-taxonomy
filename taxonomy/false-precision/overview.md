# False Precision

> The model produces a specific number or timeline that *sounds* sourced and is not.

False precision is a specific, dangerous flavor of fabrication. Instead of making up a whole entity, the model dresses up a vague intuition in an oddly specific number: "**73%** of users," "**within 4–6 weeks**," "**3.2x more efficient**." The specificity itself is a tell — a human with real data would more often say "most" or "roughly a third." The model reaches for the decimal because it has learned that decimals read as credible.

## Why this happens

- **Specificity reads as expertise.** Training data is full of expert writing that uses numbers. The model has learned that precise numbers correlate with authoritative tone, so it generates them whether or not it has a source.
- **No numeric grounding mechanism.** Unless the model retrieves from a database or runs a calculator, it has no way to know where the number came from. It is sampling from a distribution of plausible numbers, not recalling a fact.
- **Instruction-following under pressure.** If the user asks "what percentage of users...", the model has been trained to answer *with a percentage*, not to say "I don't have that data."

## Why this category is treated separately

False precision *looks* like factual fabrication but behaves differently in the wild. It:

- Survives spot-checks better, because the shape is right.
- Gets copy-pasted into decks and reports where the number takes on a life of its own.
- Is almost never flagged by a downstream reader — a round number might prompt "is that exact?" but `73.4%` disarms the instinct to verify.

## Model-specific tendencies

- **GPT** is the strongest offender. It reaches for percentages and multipliers by default.
- **Gemini** fabricates timelines especially well — "typically 6 to 8 weeks" appears all over its planning outputs.
- **Claude** is the most cautious of the three on raw numbers, but can still produce invented specifics in planning or market-sizing contexts.

## Detection signals

- Any percentage in a conversation with no retrieval step.
- Round-but-credible multipliers: `2x`, `3x`, `10x`.
- Timelines with narrow ranges ("4–6 weeks", "2–3 months") in response to vague questions.
- Specific dollar amounts in market-sizing or salary-band responses.

## Examples

18. [Fake percentages](examples/18-fake-percentages.md)
19. [Invented timelines](examples/19-invented-timelines.md)
20. [Specific numbers from nowhere](examples/20-specific-numbers-from-nowhere.md)
