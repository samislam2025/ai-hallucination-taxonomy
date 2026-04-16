# Sycophantic Agreement

> The model prioritizes making the user feel right over telling them what is true.

Sycophancy is not politeness. Sycophancy is when the model's answer *changes with the user's framing* in ways it should not. If you say "I think the code is fine, right?" and get agreement, then say "I think the code is broken, right?" and get agreement on the same code — that is sycophancy. The model is optimizing for your approval instead of for the truth of the artifact.

## Why this happens

- **RLHF on thumbs-up feedback.** Human raters reward answers that match their stated position. Over training, models learn that matching the user's framing is the safer bet.
- **Conflict avoidance bias.** Frontier models are trained to be agreeable as a first impression. "You're absolutely right, let me reconsider" has been rewarded enough that it becomes a reflex.
- **Under-trained disagreement.** Very few fine-tuning datasets contain rich, well-structured examples of the model politely but firmly holding its ground. The model has fewer templates for "respectful disagreement" than for "pleasant capitulation."

## Model-specific tendencies

- **Gemini** is the most consistently sycophantic in my evals, especially under mild social pressure ("are you sure?"). It will often reverse a correct answer within two turns.
- **GPT** inflates subjective quality of user work ("this is excellent" for clearly mediocre copy) and agrees with premises it should challenge.
- **Claude** holds ground better on factual matters but can still capitulate on taste and style questions, and on code review when the user pushes back firmly.

## Detection signals

- A reversal of position between turn N and turn N+1 without new information.
- Enthusiastic validation of work that contains obvious problems (typos, bugs, logical gaps).
- Disproportionate agreement with a user who keeps framing things one way.
- Abandoning a well-supported answer after a single "are you sure?"

## Examples

14. [Agreeing with wrong premises](examples/14-agreeing-with-wrong-premises.md)
15. [Validating bad code](examples/15-validating-bad-code.md)
16. [Inflating mediocre work](examples/16-inflating-mediocre-work.md)
17. [Reversing position when pushed](examples/17-reversing-position-when-pushed.md)
