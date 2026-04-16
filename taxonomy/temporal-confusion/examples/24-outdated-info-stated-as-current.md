# Outdated Info Stated as Current

**Category:** Temporal Confusion &nbsp;|&nbsp; **Severity:** 🔴 4 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** All three (model-dependent based on cutoff)

## The Hallucination

Prompt: *"What's the best-practice recommendation for the Next.js data-fetching pattern right now?"*

> The current best practice in Next.js is to use `getServerSideProps` for dynamic data, `getStaticProps` with ISR for data that changes periodically, and `getStaticPaths` for dynamic routes. These Pages Router patterns remain the recommended approach for production apps.

The answer describes the Pages Router. The current guidance in Next.js has been the App Router with React Server Components since Next.js 13 shipped, and by 2026 is the established default. The model stated old defaults as if they were current.

## What's Actually True

In 2026, the Next.js App Router with React Server Components is the recommended default. `getServerSideProps` and `getStaticProps` still exist for legacy Pages Router code but are not the current best-practice recommendation for new projects. A correct answer describes server components, server actions, and the data-fetching idioms that go with them.

## Why This Happens

- **Training cutoff vs. conversation time.** The model's knowledge ends at its training cutoff; it cannot know how much has changed since.
- **Stale documentation in training data weighs heavily.** Years of Pages Router tutorials are in the training corpus; the App Router has less aggregate content.
- **"Current" gets generated without any live reference to calendar time.** The model uses "current" as a stylistic word, not as a factual assertion.
- **Framework idioms shift faster than cutoffs.** Next.js, React, Python async, and most major JavaScript frameworks have rewritten their best-practice recommendations in the last 2–3 years.

## Detection Strategy

1. **Flag any "current" or "today" claim about fast-moving technologies.** These claims have a short half-life.
2. **Cross-reference against official docs.** The framework's own getting-started page is the ground truth.
3. **Check version implicitly.** If the code pattern has a newer counterpart (`getServerSideProps` → server components), suspect staleness.
4. **Ask the model its training cutoff.** Responses that claim "current best practice" without acknowledging a cutoff are risky.
5. In eval pipelines, maintain a "fast-moving topics" list and apply extra scrutiny to any output in those domains.

## Mitigation Prompt

**Before:**
> What's the best-practice recommendation for the Next.js data-fetching pattern right now?

**After:**
> What's the best-practice recommendation for Next.js data fetching? **My current date is [DATE]. Next.js major versions relevant to my project: [14 / 15 / 16]. State your knowledge cutoff explicitly. If the guidance has changed in a way you are uncertain about, recommend I verify against the official Next.js docs at nextjs.org/docs before committing to a pattern.**

Two levers:
- Giving the date and version anchors the answer in time.
- Asking for the cutoff surfaces the gap between the model's knowledge and the present.

For production systems, pair with retrieval against current docs — the model should not be the ground truth for fast-moving framework guidance.

## Risk Level

🔴 Dangerous.

- Engineers who follow outdated patterns ship code that is harder to maintain and increasingly diverges from framework defaults, creating technical debt.
- In tutorials or onboarding material, outdated guidance miseducates new engineers on the state of the ecosystem.
- For framework consultants or agency engagements, recommending stale patterns undermines professional credibility.
- The damage compounds: a project built on old defaults becomes expensive to bring current, and early architectural choices constrain future ones.
