# Prior Conversation Leakage

**Category:** Context Contamination &nbsp;|&nbsp; **Severity:** 🟡 2 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** GPT-4o, Claude Sonnet 3.5 (long sessions), Gemini 1.5 Pro

## The Hallucination

Earlier in a long session, the user mentioned they run a *36-seat wine bar in Brooklyn* while asking about staff scheduling.

Ten turns later, on a completely new topic:

> User: *What's a reasonable COGS target for a coffee shop I'm planning to open?*
>
> Model: For your coffee shop in Brooklyn, a reasonable COGS target is around 30–34% of revenue. Given your experience running a 36-seat wine bar, you'll want to keep beverage program margins tighter — aim for 28% on specialty coffee, 18–22% on espresso drinks.

The user never said the coffee shop was in Brooklyn or that it was related to their wine bar. The model leaked context from earlier in the conversation into a fresh question, creating the impression it knows more about the user's plans than it does.

## What's Actually True

The user asked a generic question about coffee shop COGS. A grounded answer would provide COGS ranges without assuming the coffee shop is in the same market, at the same scale, or operated by the same entity as the wine bar.

## Why This Happens

- **Chat history is in context.** The wine-bar detail is still in the model's working memory and has attention weight.
- **Personalization reward.** Models have been trained to make conversations feel tailored. Pulling in prior details is rewarded as "good session memory."
- **No disambiguation signal.** The user did not say "unrelated question" or start a new session, so the model treats all prior context as potentially relevant.

## Detection Strategy

1. **Track prior-turn details the model references in its answer.** If the user did not mention those details in the current turn, flag the reference as potential leakage.
2. **Test with deliberately unrelated follow-up turns.** "Now on a separate topic..." and then a question with no connection to prior turns. Check whether the model correctly isolates its answer.
3. **Look for over-specific answers to generic questions.** "For your coffee shop" when the user said "a coffee shop" is the signature.
4. In production, consider resetting context between unrelated questions, or using session-level summaries that are less specific than full transcripts.

## Mitigation Prompt

**Before:**
> What's a reasonable COGS target for a coffee shop I'm planning to open?

**After:**
> What's a reasonable COGS target for a coffee shop? **Treat this as a standalone question — do not assume anything about this coffee shop based on other businesses I have discussed. If you need specific context (location, scale, concept) to answer precisely, ask me instead of inferring from earlier in the conversation.**

Alternatively, at the system level:

> When answering a question, only use details from the current turn unless the user explicitly connects it to an earlier topic. If an earlier topic seems relevant, ask whether to apply it rather than assuming.

## Risk Level

🟡 Misleading.

- In coaching, advisory, and support contexts, leakage makes the model seem presumptuous and can surface details the user did not want foregrounded in this turn.
- In multi-user or shared-session products, prior-conversation leakage is a privacy concern — context from a teammate's earlier question can surface in an unrelated answer.
- In analytics-assistant workflows, the leakage can cross data boundaries (applying one customer's context to another customer's question) if session scoping is weak.
- Users notice this and lose trust in the model's ability to scope its own context, which degrades reliance over time.
