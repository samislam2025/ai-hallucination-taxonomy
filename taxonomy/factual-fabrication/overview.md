# Factual Fabrication

> The model produces a specific, checkable claim — a citation, a statistic, a product, an API, an event — that does not exist.

This is the category most laypeople mean when they say "hallucination." It is also the category that does the most reputational damage per incident, because fabrications are usually **plausible, specific, and confidently stated**. A fabricated statistic reads like a real one. A fabricated citation formats like a real one. A fabricated API endpoint often compiles.

## Why this happens

Transformer-based LLMs are trained to produce the most statistically likely next token, not the most *true* one. When the training distribution contains thousands of examples of citations, statistics, and API calls that look a certain way, the model can generate syntactically perfect instances of that pattern even when no grounded instance exists. Three mechanisms dominate:

1. **Pattern completion under pressure.** A well-formed prompt that ends "According to a 2021 study by..." creates strong downstream pressure to *produce something* in the shape of a study. The model has a prior that studies exist here, and will invent one before admitting it does not know.
2. **Rare-entity smoothing.** Real but low-frequency entities (obscure SDK methods, niche academic papers, regional historical events) get "averaged" with nearby high-frequency entities. The output looks correct because it sits in the right neighborhood of the latent space.
3. **Instruction-following over calibration.** RLHF rewards answering. Saying "I do not know" is trained against in many finetuning pipelines, especially on tasks that look answerable.

## Frequency across models

| Failure mode | Claude | GPT | Gemini |
|---|---|---|---|
| Invented citations | Occasional | **Common** | Occasional |
| Fake statistics | Occasional | **Common** | Common |
| Nonexistent products | Rare | **Common** | Occasional |
| Fabricated events | Rare | Occasional | Occasional |
| Phantom API endpoints | Occasional | Common | **Common** |

In my eval runs, **GPT (4 / 4o) was the most prolific fabricator** when the prompt asked for specific examples. Claude tended to hedge or refuse more readily. Gemini's fabrications clustered in code and API contexts, where its willingness to "complete the pattern" produced SDK methods that never existed.

## Detection signals

- Any specific number, date, name, or URL that the model did not have a source for in the conversation.
- "According to..." phrasing without a retrieval step preceding it.
- Code that uses an import or method not present in the library's actual public surface.
- Sources formatted consistently but not verifiable on the web.

## Examples

1. [Invented citations](examples/01-invented-citations.md)
2. [Fake statistics](examples/02-fake-statistics.md)
3. [Nonexistent products](examples/03-nonexistent-products.md)
4. [Fabricated historical events](examples/04-fabricated-historical-events.md)
5. [Phantom API endpoints](examples/05-phantom-api-endpoints.md)
