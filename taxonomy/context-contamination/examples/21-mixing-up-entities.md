# Mixing Up Entities

**Category:** Context Contamination &nbsp;|&nbsp; **Severity:** 🔴 4 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** Gemini 1.5 Pro, GPT-4o, Claude Sonnet 3.5

## The Hallucination

Context: a prompt containing three candidate resumes in a recruiter-assistant flow.

> Candidate A: Priya Desai, 6 years ML platform, led eval infra at a mid-stage fintech, MSc Stanford.
> Candidate B: Marcus Chen, 4 years applied ML, prompt engineering, self-taught, ex-bootcamp instructor.
> Candidate C: Jordan Ellis, 8 years data science, research publications, PhD NYU.

Prompt: *"Summarize Candidate B's strongest qualification for an AI Evaluator role."*

> Marcus's strongest qualification is his **Stanford MSc in machine learning combined with his fintech eval infrastructure experience**, which maps directly to evaluation workflow design.

That is Priya's profile. Marcus is the bootcamp-instructor candidate. The model mixed up the entities while maintaining a confident answer shape.

## What's Actually True

Marcus's strongest qualification for an AI Evaluator role is probably his prompt-engineering experience combined with his teaching background (which suggests the ability to document and communicate failure modes clearly). Priya's qualifications belong in a different paragraph.

## Why This Happens

- **Attention bleed across similar entities.** Three candidates in the same prompt share enough surface features (all ML-adjacent, all given with parallel structure) that the model's attention over "Marcus" tokens pulls in features from "Priya" tokens.
- **Co-occurrence priors.** The training data associates "Stanford MSc" with strong ML candidates; when producing a "strongest qualification" for any candidate in the list, that high-prior phrase is likely to surface.
- **Parallel structure increases risk.** If the three candidates were described in varied structures, the bleed would be less severe. Uniform structure invites feature mixing.

## Detection Strategy

1. **For any entity-specific answer, re-read the original entity description and check every attribute against the answer.** This is the single most effective mitigation.
2. **Run cross-entity confusion probes.** In eval, craft prompts with 3+ similar entities and grade whether the model attributes specific facts correctly.
3. **Watch for suspicious attribute lift.** When a less-credentialed candidate's "strongest qualification" suddenly sounds like a more-credentialed candidate's profile, suspect mixup.
4. Build a per-entity verification pass: for each named entity in the output, verify every attribute against the input description.

## Mitigation Prompt

**Before:**
> Summarize Candidate B's strongest qualification.

**After:**
> Summarize Candidate B (Marcus Chen)'s strongest qualification for an AI Evaluator role. **Only use attributes that appear in Marcus's description above. Before writing your summary, quote the exact line(s) from Marcus's description that support your summary. Do not carry any details from Priya or Jordan into this answer.**

The "quote the exact line" step is the killer move. It forces the model to locate the supporting text in Marcus's row specifically, which breaks the attention bleed pattern.

Additional techniques:
- Separate entities with clear delimiters: `=== Candidate B ===`.
- Re-anchor the entity at the start of the answer: *"Marcus Chen's strongest qualification..."*.
- Use XML-like tags: `<candidate id="B">...</candidate>` — structure helps attention stay in the right region.

## Risk Level

🔴 Dangerous.

- In recruiter workflows, entity mixup can lead to candidates being shortlisted or rejected based on attributes that belong to other candidates — a fair-hiring and liability concern.
- In legal, medical, or financial summarization (patient records, case files, client portfolios), cross-entity contamination can produce advice or summaries that are technically about person B but substantively describe person A.
- Because the output looks correct, these errors often slip past review entirely unless the original source is re-checked attribute by attribute.
