# Phantom API Endpoints

**Category:** Factual Fabrication &nbsp;|&nbsp; **Severity:** ⛔ 5 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** GPT-4o, Gemini 1.5 Pro, Gemini 2.0, Claude Sonnet 3.5 (less often)

## The Hallucination

Prompt: *"Write a Python script that uses the Stripe SDK to list all subscriptions that will renew in the next 7 days."*

```python
import stripe
from datetime import datetime, timedelta

stripe.api_key = "sk_..."

# Fabricated method on stripe.Subscription
upcoming = stripe.Subscription.list_upcoming_renewals(
    within_days=7,
    status="active",
    limit=100,
)

for sub in upcoming.data:
    print(sub.id, sub.next_renewal_at)
```

`stripe.Subscription.list_upcoming_renewals()` does not exist. The Stripe SDK does not have a `list_upcoming_renewals` method, a `within_days` parameter, or a `next_renewal_at` field on the subscription object. Every identifier here is invented. But the code *looks right*, passes a glance review, and will only fail at runtime with an `AttributeError`.

## What's Actually True

The correct approach uses `stripe.Subscription.list()` with `status="active"` and filters client-side on `current_period_end`, comparing it to `now + 7 days`. The SDK documentation has never exposed a one-shot "upcoming renewals" helper.

## Why This Happens

- **Pattern-completion over API grounding.** The model has seen enough SDK code that it generates the *shape* of a clean solution. When a clean helper method does not exist, it invents one.
- **RLHF reward for terse answers.** A one-liner that "solves" the task scores higher than a longer, correct loop that filters manually.
- **Plausible naming.** `list_upcoming_renewals` is exactly what a well-designed SDK method would be named, which is why this fabrication is so successful.

## Detection Strategy

1. **Never trust an SDK method you have not seen in the vendor's docs in the last 30 days.** Open the docs on the side while reviewing AI-generated code.
2. **Run the code.** An `AttributeError` is the fastest possible signal.
3. **Diff the model's imports against the library's public API surface.** Tooling like `pydoc` or TypeScript's auto-complete is a strong first line of defense.
4. Watch for invented parameter names on real methods — this is harder to catch than invented methods.
5. In eval pipelines, include a static check that exercises every SDK call against the installed library version.

## Mitigation Prompt

**Before:**
> Write a Python script that uses the Stripe SDK to list upcoming renewals.

**After:**
> Write a Python script using the Stripe SDK to find subscriptions renewing in the next 7 days. **Only use public methods that exist in the `stripe-python` SDK. If no single method exists for this use case, do the filtering client-side rather than inventing a convenience method.** When you cite a method, if you are unsure whether it exists, add a `# TODO: verify this method` comment and explain what it is supposed to do.

The "add a TODO comment if unsure" pattern is valuable — it converts the fabrication into a self-flagged TODO, which is much easier to catch in code review than a clean-looking line.

## Risk Level

⛔ Critical.

- Phantom APIs can make it into merged pull requests when the reviewer does not run the code locally. In CI-only environments, a runtime error in a rarely-exercised branch can ship to production.
- In security-sensitive code (auth, billing, key management), a phantom API call could fail silently or be caught by overly-broad exception handlers, masking real logic.
- This is the failure mode that most damages trust in AI-assisted coding. A single phantom-API incident can cost a team months of adoption momentum.
