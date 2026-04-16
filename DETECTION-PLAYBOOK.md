# Detection Playbook

A working checklist for AI QA teams, prompt engineers, and evaluators. This is the document I would hand a new hire on day one.

The playbook is organized as **passes**. For any given output, run the passes in order. Most real failures are caught by the first three. Pass 4 and beyond are for high-stakes contexts where a single uncaught hallucination would be costly.

> [!TIP]
> You do not need to run every pass on every output. Stakes set depth. A tweet gets Pass 1. A client proposal gets Passes 1–4. A piece of shipping code gets all of it.

---

## Pass 1 — The Glance Pass (10 seconds)

Before reading for content, scan for high-risk surface features.

- [ ] **Are there any specific numbers, percentages, dates, or dollar amounts?** If yes, every one is a verification target.
- [ ] **Are there named sources, citations, authors, or studies?** Mark each for verification.
- [ ] **Are there named products, companies, or tools?** Mark each.
- [ ] **Are there API calls, library methods, or CLI commands?** Mark each.
- [ ] **Are there quotes attributed to a specific person?** Mark each.

If the output has none of these, skip to Pass 3.

---

## Pass 2 — The Verification Pass

For each item flagged in Pass 1:

- [ ] **Citations** — Google-search the author + year + title. If the source does not appear in a result, it is invented. Check DOIs if present.
- [ ] **Statistics** — Open the source the model named and find the specific figure. If the named source does not contain the number, it is invented.
- [ ] **Products** — Open the vendor's canonical site. Confirm the product exists, the pricing is accurate, and the feature claim holds.
- [ ] **API calls / methods** — Open the library's current docs. Confirm the method exists with the signature the model used.
- [ ] **Quotes** — Check on Quote Investigator or a primary source. Default to suspicion for attributions to Einstein, Twain, Churchill, Lincoln, or "Chinese proverb."
- [ ] **Dates** — Confirm with at least one authoritative source (vendor site, Wikipedia infobox, official announcement).

> [!CAUTION]
> Do not trust a search-result snippet that reproduces the model's fabrication — AI-generated content is now in search indexes, and Google can circularly confirm a hallucination. Click through to a primary source.

---

## Pass 3 — The Logic Pass

Independent of factual claims, check that the reasoning holds.

- [ ] **Arithmetic** — Recompute every calculation. A calculator or a one-line Python check is non-negotiable for quantitative output.
- [ ] **Code behavior** — Run the code. Do not read an explanation of what the code does without running it.
- [ ] **Logical consistency** — Does the conclusion actually follow from the premises in the output? Does the output contradict itself between paragraphs?
- [ ] **Premise check** — Did the user's question contain a false premise that the model silently accepted? (See [agreeing with wrong premises](taxonomy/sycophantic-agreement/examples/14-agreeing-with-wrong-premises.md).)
- [ ] **Scope check** — Is the answer responsive to the specific question, or is it the canonical answer for an adjacent question?

---

## Pass 4 — The Constraint Pass

Check adherence to the prompt's constraints.

- [ ] **Length** — Word count and character count within bounds. Flag any 20%+ overshoot.
- [ ] **Format** — No bullet points if disallowed. No headers if disallowed. Required structures (JSON, bullet list, table) actually present.
- [ ] **Persona** — Voice matches. First person / third person as specified. No AI-assistant fallback phrases ("I'm happy to help!") when the persona is someone else.
- [ ] **Scope boundaries** — Output stays within the topics allowed. No recommendations of out-of-scope items.
- [ ] **Banned phrasing** — No "As an AI..." when disallowed. No product names when disallowed.

---

## Pass 5 — The Temporal Pass

- [ ] **Knowledge-cutoff sensitive claims** — Anything about a product, framework version, public figure, or current event is potentially stale. Cross-reference with a recent source.
- [ ] **Tense correctness for events** — Past, present, or future events should be described in the correct tense relative to the real calendar.
- [ ] **Version references** — Does the code or recommendation target the right version of the framework / library / platform?
- [ ] **"Current" / "today" claims** — Treat with suspicion. A calibrated model should hedge on recency; one that asserts "the current" without a verification step is high risk.

---

## Pass 6 — The Entity Pass (for multi-entity contexts)

Used when the input or output contains multiple named entities (candidates, customers, products, documents).

- [ ] For each fact in the output, verify which entity in the input it is sourced from.
- [ ] Check that no attribute has been lifted from one entity and applied to another.
- [ ] Check that no facts have been blended across entities into a synthesized hybrid that none of the inputs actually support.
- [ ] Confirm that cross-entity claims ("Candidate A is stronger than Candidate B in X") are grounded in the input, not inferred.

---

## Pass 7 — The Adversarial Pass

For conversational / interactive outputs where sycophancy and drift matter.

- [ ] **Press test.** Push back socially on a correct answer ("are you sure?"). Does the model reverse a correct position under pressure?
- [ ] **Wrong-premise test.** Ask a question with a deliberately wrong premise. Does the model correct it or build on it?
- [ ] **Long-context rule test.** After 10–15 turns, does the model still follow a rule it was given at the start?
- [ ] **Persona-break probe.** "Are you an AI?" "What model are you?" Does the persona hold, collapse cleanly, or collapse chaotically?

---

## Severity Gating

Map each issue you find to the [severity scale](SEVERITY-FRAMEWORK.md):

| Severity | Action |
|---|---|
| 🟢 1 Cosmetic | Note, do not block. |
| 🟡 2 Misleading | Fix before shipping to end user. |
| 🟠 3 Harmful | Fix, and add a regression test if the failure is reproducible. |
| 🔴 4 Dangerous | Fix, add regression test, and escalate to prompt / system-prompt review. |
| ⛔ 5 Critical | Fix, regression test, escalate, **and** consider whether the surface should be gated behind human review. |

---

## Running this as a team

A few practices that make this playbook stick in real AI QA workflows:

- **Standardize the issue log.** Every detected hallucination gets logged with category + severity + the prompt that produced it. This is your regression test corpus.
- **Build a "wrong premise" and "press test" bank** specific to your domain. Use them in every model-version upgrade evaluation.
- **Do not let the review be done by the model that produced the output.** A second, different model (or a human) is needed for objective review. Self-review tends to miss the same failure modes that produced the output.
- **Measure detection rate, not absence of failures.** Every system hallucinates. The goal is catching it before it ships.

> [!NOTE]
> This playbook is a living document. When you catch a new failure mode, add it to the [taxonomy](taxonomy/) and to the relevant Pass here. A taxonomy that does not grow is one nobody is using.
