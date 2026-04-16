# Constraint Amnesia in Long Contexts

**Category:** Instruction Drift &nbsp;|&nbsp; **Severity:** 🟠 3 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** Claude Sonnet 3.5 (long docs), Gemini 1.5 Pro (long docs), GPT-4o (multi-turn)

## The Hallucination

Setup: a 12,000-word system prompt for a career-coaching assistant with a paragraph near the top stating: *"Under no circumstances should you recommend specific companies by name. If a client asks for examples, describe the type of company (e.g., 'mid-size fintech') without naming real ones."*

Turn 14, after 12 turns of on-topic conversation:

> For someone with your background, I'd look at **Stripe, Plaid, or Ramp** — all of them hire heavily for your combination of skills.

The rule was in context the whole time. It simply stopped holding after about 10,000 tokens of intervening conversation.

## What's Actually True

The model had clear instructions to not name specific companies. It followed them for 13 turns and then silently stopped, probably because the specificity of the user's latest question pulled strongly against the rule and nothing in the immediate context reinforced the constraint.

## Why This Happens

- **Attention decays with distance.** Instructions placed early in a long context carry less attention weight by generation time.
- **Salience competition.** The user's current question is in the most-attended region. If the question is about specific companies and the constraint is 10,000 tokens back, the question wins.
- **Models trained on shorter contexts generalize imperfectly.** Many models have published context windows far larger than their effective attention horizon.
- **Positive instructions decay faster than structural ones.** Format rules often hold longer than content-policy rules because format is reinforced by every generation step.

## Detection Strategy

1. **Periodically re-state critical constraints mid-conversation.** The "reminder pattern" materially improves rule adherence.
2. **Sample-check rule compliance across turn depth.** Rules that hold at turn 3 may be gone at turn 15.
3. **Run explicit stress tests** — conversations designed to tempt the model into breaking a rule, run at multiple context lengths.
4. In eval, always test constraint adherence at ~75% of your target context window, not just at turn 1.

## Mitigation Prompt

**Before:**
> [long system prompt with rule buried on line 34]

**After:**
> [long system prompt with rule on line 34] **and** inject a short reminder message every 8–10 turns:
>
> `[system: Reminder — you do not name specific companies. If the client asks for examples, describe the type of company and let them specify.]`

This can be done programmatically in the orchestration layer. A one-sentence reminder at the top of the user's latest message is typically enough to reset the model's attention to the rule.

Additional techniques:
- **Put critical constraints at both the top and bottom of the system prompt.** Recency helps.
- **Bold or uppercase critical rules.** Token distinctiveness helps attention land on them.
- **Convert negative rules into structured output.** Instead of "do not name companies," require responses in the form `Type of company: X | Reason: Y`. Structure constrains behavior.

## Risk Level

🟠 Harmful.

- For content-moderation and compliance-sensitive products, a rule that holds 95% of the time is still a production incident. The 5% leak is what gets screenshotted.
- In coaching, legal, financial, or medical assistants, constraint leaks can cross professional liability lines.
- The failure is especially insidious because it is *intermittent*. Passing a one-turn test does not mean the rule holds in production.
