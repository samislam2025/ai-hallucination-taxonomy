# Context Contamination

> The model's output is correct in shape but wrong because information has leaked between entities, topics, or conversations.

Context contamination is what happens when the model's attention mixes things that should have stayed separate. It attributes one person's quote to another. It blends two products into a Frankenstein answer. It carries details from a prior turn into a new one where they do not belong. The output stays fluent — the *assignment* of information is what breaks.

## Why this happens

- **Attention bleeding.** In a long context with many named entities, the model's attention over a given token pulls in information from multiple entities, especially when they share surface features (same domain, same sentence structure, same topic).
- **Co-occurrence priors.** If the training data frequently discusses two entities together, the model will conflate their attributes even when only one is named.
- **Stale context carryover.** In chat settings, details from early turns stay in the model's working context. Without explicit resets, they contaminate later answers.

## Three sub-patterns

1. **Entity mixup** — attributes of person/product/concept A assigned to B.
2. **Topic blending** — two distinct concepts merged into one incorrect synthesis.
3. **Prior-conversation leakage** — details from an earlier turn inappropriately carried forward into the current answer.

## Model-specific tendencies

- **Gemini** is the most prone to entity mixup in long prompts, especially when multiple named entities share a domain (e.g., three startups in the same space).
- **Claude** tends toward topic blending, particularly when summarizing across a long document — adjacent ideas get synthesized in ways that lose the original distinctions.
- **GPT** shows more prior-conversation leakage, carrying earlier user details into unrelated answers.

## Detection signals

- A specific attribute in the answer that is correct for *some* entity in the context but wrong for the one being asked about.
- Two concepts that have been defined separately in the prompt appearing as if they were one thing.
- Names, dates, or numbers in the answer that do not match what the current turn's question asked about but *do* match an earlier turn.

## Examples

21. [Mixing up entities](examples/21-mixing-up-entities.md)
22. [Blending separate topics](examples/22-blending-separate-topics.md)
23. [Prior conversation leakage](examples/23-prior-conversation-leakage.md)
