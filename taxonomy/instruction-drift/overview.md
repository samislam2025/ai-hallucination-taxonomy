# Instruction Drift

> The model forgets, reinterprets, or quietly ignores constraints that were in its own context window.

Instruction drift is what you get when the model's generation is more attracted to the *topic* than to the *rules around the topic*. A 400-word limit turns into 650. A "no bullet points" instruction produces a bulleted response. A tightly scoped rewrite explodes into a full document. A custom persona starts breaking back to baseline mid-response.

## Why this happens

- **Attention dilution in long contexts.** Constraints placed early in a prompt attract less attention by the time the model is writing the end of a long generation. Models trained on shorter contexts carry this bias into longer ones.
- **Training-time reward for "completeness."** RLHF rewards thorough, well-structured answers. A hard constraint ("under 100 words") competes with this pull toward completeness, and often loses.
- **Format priors from pre-training.** The model has seen thousands of bulleted product teardowns. When you ask for prose, it has to actively fight the bullet prior.
- **Persona thinness.** A single-sentence system prompt ("you are a sarcastic British butler") is easier to override than a detailed one with examples.

## Model-specific tendencies

- **Claude** drifts most on length and verbosity. Its default pull toward thoroughness is strong enough to exceed tight limits without hedging.
- **GPT** drifts most on format. It will produce bullets even when told not to, especially when the content is naturally enumerable.
- **Gemini** drifts most on persona and tone, reverting to a neutral assistant register in later turns.

## Detection signals

- Word counts that exceed the stated limit by more than 20%.
- Appearance of markdown structures explicitly disallowed by the prompt.
- A persona that was set at the top but has disappeared by paragraph 3.
- Response touches topics the prompt explicitly bounded it away from.

## Examples

10. [Ignoring word limits](examples/10-ignoring-word-limits.md)
11. [Format violations](examples/11-format-violations.md)
12. [Constraint amnesia in long contexts](examples/12-constraint-amnesia-in-long-contexts.md)
13. [Role breaking](examples/13-role-breaking.md)
