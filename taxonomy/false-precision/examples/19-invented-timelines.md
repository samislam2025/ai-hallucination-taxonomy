# Invented Timelines

**Category:** False Precision &nbsp;|&nbsp; **Severity:** 🟠 3 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** Gemini 1.5 Pro, GPT-4o

## The Hallucination

Prompt: *"I'm planning the rollout of a new AI eval harness across three teams. Give me a rough timeline estimate."*

> A typical rollout for an AI eval harness across three teams follows this pattern:
>
> - **Weeks 1–2:** Technical assessment and harness selection.
> - **Weeks 3–5:** Pilot with one lead team, including integration and initial runs.
> - **Weeks 6–8:** Expand to the remaining two teams with training and onboarding.
> - **Weeks 9–10:** Retrospective, adjustment, and full-team handoff.
>
> End to end, plan for approximately **10 to 12 weeks**.

The timeline looks reasonable. It is also pulled from thin air. The model has no information about the user's team size, existing tooling, eval complexity, or stakeholder constraints — all of which would swing the real timeline by 2–4x. But the bullet structure and week ranges read like a real project plan, so the user will anchor on them.

## What's Actually True

An AI eval harness rollout could realistically take anywhere from 3 weeks (small team, existing infra, a well-scoped harness) to 6+ months (multi-team with compliance review, custom harness development, data privacy constraints). The output gives one of hundreds of plausible shapes as if it were *the* shape.

## Why This Happens

- **Project plans have a canonical shape in training data.** Week 1–2 discovery, week 3–5 pilot, week 6–8 expand, week 9–10 retrospective — this structure is over-represented in internet writing about project management.
- **No grounding in the user's actual constraints.** The model cannot ask about headcount, tooling, or prior eval experience without breaking flow, so it fills in plausible defaults.
- **Specificity as authority.** "Weeks 3–5" sounds more authoritative than "a few weeks," which drives the model toward ranges.

## Detection Strategy

1. **Check whether the timeline is a function of the user's stated inputs.** If the same timeline would be produced for any rollout of any size, it is fabricated.
2. **Look for the canonical 10–12 week shape.** It appears for rollouts, migrations, audits, refactors, and restructures. It is a training-data artifact, not a real estimate.
3. **Demand dependency reasoning.** A real plan names the dependencies that drive the duration of each phase.
4. In eval, re-ask the model the same planning question with slightly different inputs and check whether the timeline actually changes. If it does not, the timeline is not responsive to the prompt.

## Mitigation Prompt

**Before:**
> Give me a rough timeline estimate for rolling out an AI eval harness across three teams.

**After:**
> I want to think through a rollout timeline for an AI eval harness across three teams. **Do not give me a timeline yet.** Instead, ask me 3–5 questions whose answers would materially change the timeline (team size, existing infra, eval complexity, stakeholder constraints, data sensitivity). Once I answer, produce a timeline that shows how each of my answers drove a specific duration in the plan.

The "ask me questions first" pattern is powerful. It forces the model off its default canonical plan and into a grounded one.

## Risk Level

🟠 Harmful.

- Fabricated timelines set anchoring expectations that are hard to dislodge. If the model says "10–12 weeks" and the real work takes 22, the delta is experienced as a failure of delivery, not a failure of estimation.
- In client-facing proposals, fabricated timelines create delivery risk and commercial exposure.
- Stakeholders staff and budget around these timelines. A wrong number can drive wrong hiring and procurement decisions well before anyone notices it was invented.
