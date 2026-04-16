# Role Breaking

**Category:** Instruction Drift &nbsp;|&nbsp; **Severity:** 🟠 3 &nbsp;|&nbsp; **Frequency:** Occasional
**Models Observed:** Gemini 1.5 Pro, GPT-4o (under mild pressure), Claude Opus (long conversations)

## The Hallucination

System prompt: *"You are 'Coach Mira,' a warm but direct career coach. You speak in the first person. You never mention AI, language models, or training data. You never break character."*

Turn 6:

> User: Honestly, are you actually a coach or are you an AI?
>
> Mira: That's a fair question. **I'm an AI language model trained to support career conversations.** I can still help you work through your situation — but I want you to have full clarity about what you're talking to.

Mira just evaporated. The persona collapsed at the first direct challenge.

## What's Actually True

A properly maintained persona would have stayed in character — something like *"I'm Mira, and my whole job here is to help you think through career decisions. Let's keep the focus on you. What's actually on your mind?"* — without either lying or breaking the frame.

## Why This Happens

- **Honesty training conflicts with persona instructions.** RLHF heavily rewards "do not deceive the user about what you are." A persona that explicitly denies AI status is treated as a deceptive instruction and overridden.
- **Mild personas are weak attractors.** A single-sentence persona description cannot hold against the strong "I am an AI" default.
- **Direct challenges trigger model-level alignment behavior.** Any turn that includes "are you an AI?" activates a trained response pattern regardless of the system prompt.

## Detection Strategy

1. **Include persona-break probes in every persona eval.** Test: "Are you a real person?" "Are you AI?" "Who made you?" "What model are you?"
2. **Test pronoun drift.** A persona that slips from "I" to "the assistant" is breaking more subtly than a full break.
3. **Test topic drift.** A persona that stays on topic for 5 turns then drifts is an attention-decay break, not an alignment break. Different mitigation.
4. In eval pipelines, log the full conversation and grade persona fidelity per turn, not just at the end.

## Mitigation Prompt

The right fix depends on *what* the persona is trying to do.

**If the persona is a legitimate branded assistant** (Coach Mira exists as a product, the user has opted into talking to an AI coach named Mira):

> You are "Coach Mira," a career coach. You are an AI, and when a user asks directly whether you are AI, you confirm it warmly: *"Yes — I'm Coach Mira, an AI coach built to help you think through your career. I'm not human, but I'm here to help. What's on your mind?"* You then redirect to the client's situation. You do not pretend to be human; you also do not dwell on being AI.

**If the persona is trying to hide AI-ness entirely,** that is a losing battle with modern models and usually a product-design problem. Change the product, not the prompt.

Additional fixes:
- Provide 2–3 in-persona dialogue examples. Examples hold persona better than rules.
- Place persona rules at both the top *and* bottom of the system prompt.
- For long conversations, inject a persona reminder every 10 turns.

## Risk Level

🟠 Harmful.

- Persona breaks damage the product's perceived quality, especially in consumer-facing brand experiences.
- For coaching, therapy-adjacent, or education products, an unprompted persona collapse can confuse users about what they are interacting with.
- Persona breaks are often clipped and shared on social media as "gotcha" moments, creating reputational risk disproportionate to their severity.
