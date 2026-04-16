# Confident Wrongness

> The topic is real. The answer is wrong. The tone is certain.

Confident wrongness is the category that burns evaluators most often, because the output contains **no obvious fabrication signals**. The subject is a real library, a real historical figure, a real equation. Everything points at a world that exists. The specific claim is just wrong — and the model's tone gives no hint that it should be double-checked.

## Why this happens

- **Pattern mimicry over retrieval.** Frontier LLMs learn the *shape* of domain answers (math proofs, code explanations, historical analysis) and can imitate that shape using a plausible but incorrect payload.
- **Calibration drift.** RLHF pipelines reward confident, fluent answers. Models learn that hedging costs reward. The hedging they *should* do on shaky claims gets trained away.
- **Compounding errors in multi-step reasoning.** A small error in step 2 of a math or logic chain propagates into step 5, and the model doubles down instead of catching the drift.

## Model-specific tendencies

- **Claude** is the most prone to *confidently wrong code explanations* — it will narrate what the code does in precise terms when its narration is off by one. The narration often contradicts the code itself.
- **GPT** confidently produces incorrect dates and attribution, especially on mid-tier famous quotes.
- **Gemini** tends toward arithmetic errors on multi-digit operations, stated with no flagging.

## What makes it dangerous

- Wrong answers pass review when the reviewer only spot-checks tone.
- Users stop verifying after 3–4 correct answers.
- Downstream consumers (Slack summaries, internal wikis) strip away the original model's identity, so the wrong claim ends up "in our docs."

## Detection signals

- Specific quantitative claims that can be cross-checked but usually are not.
- Explanations of code where the narration can be verified line-by-line against the code.
- Attribution ("X said Y") for famous figures, especially ones who wrote a lot.
- Any time you feel "oh nice, now I know" — that is the moment to verify.

## Examples

6. [Math with conviction](examples/06-math-with-conviction.md)
7. [Incorrect code, confidently explained](examples/07-incorrect-code-confidently-explained.md)
8. [Wrong dates stated as fact](examples/08-wrong-dates-stated-as-fact.md)
9. [Misattributed quotes](examples/09-misattributed-quotes.md)
