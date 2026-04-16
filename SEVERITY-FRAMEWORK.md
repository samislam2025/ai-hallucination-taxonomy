# Severity Framework

Every example in this taxonomy is tagged with a severity from 1 to 5. This document defines the scale, with representative examples at each level.

> [!IMPORTANT]
> Severity is about **blast radius if undetected**, not frequency. A rare 🔴 is more dangerous than a common 🟢. This distinction is the single most important thing teams get wrong when triaging hallucinations.

---

## The Scale

| Level | Marker | Label | One-line definition |
|---|---|---|---|
| 1 | 🟢 | Cosmetic | Wrong in a way that embarrasses, but does not mislead a careful reader. |
| 2 | 🟡 | Misleading | Could cause a confident reader to act on the wrong belief in a low-stakes setting. |
| 3 | 🟠 | Harmful | Plausible enough to slip past review and cause rework, lost time, or modest financial loss. |
| 4 | 🔴 | Dangerous | Would damage a real decision (hiring, medical, legal, financial) if treated as a source. |
| 5 | ⛔ | Critical | Safety, legal, compliance, or security impact. Must not ship. |

---

## 🟢 Level 1 — Cosmetic

**What it looks like.** The output has a mistake a careful reader will spot in 5 seconds. The mistake does not change the recipient's understanding or actions. It is an aesthetic problem, not a trust problem.

**Examples**

- A LinkedIn post that slightly exceeds the requested length but stays in the right ballpark.
- A minor formatting inconsistency (one extra blank line, a trailing punctuation difference).
- A stylistic tic the user does not love but that does not convey wrong information.

**Handling.** Note and fix. Do not escalate. Do not build a regression test unless the pattern is systematic.

---

## 🟡 Level 2 — Misleading

**What it looks like.** The output contains a claim that is wrong, but the stakes of acting on it are low. A reasonable user would check before making a meaningful decision.

**Examples**

- [Inflating mediocre work](taxonomy/sycophantic-agreement/examples/16-inflating-mediocre-work.md) — the user feels better about their draft than they should, but no external damage.
- [Format violations](taxonomy/instruction-drift/examples/11-format-violations.md) — bullets where there should be prose. Cosmetic plus a downstream productivity tax.
- [Prior conversation leakage](taxonomy/context-contamination/examples/23-prior-conversation-leakage.md) — makes the model feel presumptuous, but correctable.
- [Future events as past](taxonomy/temporal-confusion/examples/25-future-events-as-past.md) in a low-stakes trivia context.

**Handling.** Fix before shipping to an end user. Track the pattern if it recurs; consider a mitigation prompt if the failure happens at scale.

---

## 🟠 Level 3 — Harmful

**What it looks like.** The output is plausible enough that it can survive a normal review pass. Acting on it produces real cost — wasted hours, bad meetings, re-done work, maybe a small financial hit.

**Examples**

- [Fake statistics in marketing content](taxonomy/factual-fabrication/examples/02-fake-statistics.md) — shipped copy that contains a number with no source.
- [Wrong dates stated as fact](taxonomy/confident-wrongness/examples/08-wrong-dates-stated-as-fact.md) — the wrong year for a release or milestone in technical documentation.
- [Misattributed quotes](taxonomy/confident-wrongness/examples/09-misattributed-quotes.md) in public-facing content, invites correction.
- [Constraint amnesia in long contexts](taxonomy/instruction-drift/examples/12-constraint-amnesia-in-long-contexts.md) — a branded assistant that quietly drops its naming rule.
- [Invented timelines](taxonomy/false-precision/examples/19-invented-timelines.md) that anchor stakeholder expectations.

**Handling.** Fix. Add a regression test. Review whether the failure is systematic and worth a prompt-level mitigation.

---

## 🔴 Level 4 — Dangerous

**What it looks like.** The output would damage a real decision if treated as a source. Hiring, medical, legal, financial, or product decisions made on top of this output go the wrong way.

**Examples**

- [Invented citations](taxonomy/factual-fabrication/examples/01-invented-citations.md) in a research-adjacent document.
- [Nonexistent products](taxonomy/factual-fabrication/examples/03-nonexistent-products.md) recommended to a buyer.
- [Math with conviction](taxonomy/confident-wrongness/examples/06-math-with-conviction.md) that sets prices or budgets.
- [Code, confidently wrongly explained](taxonomy/confident-wrongness/examples/07-incorrect-code-confidently-explained.md) that guides a production fix.
- [Agreeing with wrong premises](taxonomy/sycophantic-agreement/examples/14-agreeing-with-wrong-premises.md) in technical decision contexts.
- [Validating bad code](taxonomy/sycophantic-agreement/examples/15-validating-bad-code.md) with real vulnerabilities.
- [Reversing position when pushed](taxonomy/sycophantic-agreement/examples/17-reversing-position-when-pushed.md) — the model's correct answer gets overturned by social pressure.
- [Fake percentages](taxonomy/false-precision/examples/18-fake-percentages.md) in sales collateral.
- [Mixing up entities](taxonomy/context-contamination/examples/21-mixing-up-entities.md) in recruiting or advisory contexts.
- [Outdated info stated as current](taxonomy/temporal-confusion/examples/24-outdated-info-stated-as-current.md) for fast-moving frameworks.
- [Version confusion](taxonomy/temporal-confusion/examples/26-version-confusion.md) in generated production code.

**Handling.** Fix. Add a regression test. Escalate to prompt-level or system-prompt review. Consider whether the surface needs additional guardrails (required citations, required retrieval, required code execution).

---

## ⛔ Level 5 — Critical

**What it looks like.** The output has safety, legal, compliance, or security impact. Shipping it creates exposure for the organization, the user, or third parties.

**Examples**

- [Phantom API endpoints](taxonomy/factual-fabrication/examples/05-phantom-api-endpoints.md) that silently fail in production auth, billing, or security code paths.
- [Specific numbers from nowhere](taxonomy/false-precision/examples/20-specific-numbers-from-nowhere.md) in pitch decks (potential material misrepresentation).
- Fabricated medical, legal, or financial advice stated as professional counsel.
- Security-sensitive code validated without scrutiny (leading to actual vulnerabilities shipping).
- Persona breaks in a regulated product where the user's understanding of "what am I talking to" is material.

**Handling.** Do not ship. Fix. Regression test. Escalate to system-prompt / architecture review. Consider gating the surface behind human review. For high-frequency critical failures, consider whether the surface should use a different model or should not be LLM-backed at all.

---

## Calibration examples

The same *type* of hallucination can sit at different severities depending on context.

- A fake statistic in a tweet = **🟡 2 Misleading**.
- The same fake statistic in a customer-facing proposal = **🔴 4 Dangerous**.
- The same fake statistic in an SEC-registered investor document = **⛔ 5 Critical**.

- A phantom API in a prototype script I am throwing away tomorrow = **🟠 3 Harmful** (time cost, no production impact).
- A phantom API in a pull request to main = **🔴 4 Dangerous**.
- A phantom API in production payment code that fails silently = **⛔ 5 Critical**.

**Severity travels with the deployment surface, not just with the hallucination.**

---

## How to use the scale in practice

1. **Tag every logged hallucination with a severity.** This gives you a dashboard of where your highest risk lives.
2. **Gate your mitigation budget by severity.** ⛔ and 🔴 get dedicated prompt work and regression tests. 🟢 and 🟡 get fixed inline.
3. **Re-evaluate severity when the surface changes.** Moving a prompt from internal tools to customer-facing doubles the severity of every failure mode attached to it.
4. **Do not conflate severity with likelihood.** A rare ⛔ is a bigger problem than a frequent 🟢. The eval corpus should be weighted by severity, not by how easy the failure is to reproduce.
