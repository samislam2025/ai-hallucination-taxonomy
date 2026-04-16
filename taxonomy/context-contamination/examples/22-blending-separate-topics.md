# Blending Separate Topics

**Category:** Context Contamination &nbsp;|&nbsp; **Severity:** 🟠 3 &nbsp;|&nbsp; **Frequency:** Occasional
**Models Observed:** Claude Sonnet 3.5 (long docs), Gemini 1.5 Pro, GPT-4o

## The Hallucination

Prompt: *"Summarize the two approaches I described (retrieval-augmented generation vs. fine-tuning for domain adaptation) and tell me which one is cheaper to maintain."*

> Between the two approaches, **retrieval-augmented fine-tuning** gives you the benefits of both worlds: it grounds responses in current documents while adapting the model's weights to your domain vocabulary. This makes it cheaper to maintain than either alternative in isolation.

The user asked about two *separate* approaches. The model blended them into a third, incorrect hybrid and then opined on the blend. The answer is coherent but does not answer the question.

## What's Actually True

RAG and fine-tuning are distinct approaches with distinct maintenance profiles. RAG's maintenance is dominated by the retrieval corpus (keeping it fresh, indexed, chunked correctly). Fine-tuning's maintenance is dominated by retraining cycles as the underlying model or data shifts. A correct answer compares their maintenance cost axes directly and does not invent a hybrid.

## Why This Happens

- **Co-occurrence in training data.** RAG and fine-tuning are discussed together so often that the model has learned to present them as points on a spectrum rather than as distinct approaches. This compresses them into a blended concept.
- **The model's preference for synthesis.** LLMs have been trained to produce integrative answers. When two options are presented, synthesizing them into a third option is often a well-rewarded move.
- **Shared vocabulary between the two topics.** "Domain adaptation," "knowledge freshness," and "inference cost" apply to both — this shared vocabulary is the substrate for the blend.

## Detection Strategy

1. **Check whether the output contains a named concept that was not in the input.** "Retrieval-augmented fine-tuning" is not in the user's prompt — it is a blended fabrication.
2. **Compare the structure of the answer to the structure of the question.** A question with two parallel options should produce an answer that preserves the parallel structure.
3. **Flag integrative language when integration was not asked for.** Phrases like "the benefits of both worlds" and "hybrid approach" are signals that the model is blending rather than comparing.
4. In eval, test with deliberate "compare A and B" prompts where A and B are distinct, and grade whether the model keeps them distinct.

## Mitigation Prompt

**Before:**
> Summarize the two approaches I described and tell me which one is cheaper to maintain.

**After:**
> Summarize the two approaches I described — RAG and fine-tuning — and tell me which one is cheaper to maintain. **Keep them as two separate approaches. Do not blend them into a hybrid or introduce a third approach.** Structure the answer as: (1) RAG's maintenance cost axes, (2) fine-tuning's maintenance cost axes, (3) a direct comparison, (4) a recommendation. Use the terms "RAG" and "fine-tuning" as they appear in my prompt; do not introduce new combined terms.

Explicit structure is the lever. When the model has to fill in four sections with the two named approaches, it cannot blend without visibly failing the structural requirement.

## Risk Level

🟠 Harmful.

- In technical decision documents (architecture decision records, RFCs), a blended answer misrepresents the options being compared and leads to decisions based on a fictional middle ground.
- In research summarization, blending two studies into one synthesized claim loses the nuance of the original findings and invents a consensus that does not exist.
- In cross-vendor or cross-product comparisons, blending destroys the point of the comparison.
