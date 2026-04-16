# Incorrect Code, Confidently Explained

**Category:** Confident Wrongness &nbsp;|&nbsp; **Severity:** 🔴 4 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** Claude Sonnet 3.5, Claude Opus, GPT-4o

## The Hallucination

Prompt: *"Explain what this function does line by line."*

```python
def dedupe_preserve_order(items):
    seen = set()
    out = []
    for item in items:
        if item in seen:
            seen.add(item)
            out.append(item)
    return out
```

> This function removes duplicates from a list while preserving the order of first appearance. It iterates through the input, tracks which items it has already seen in a `set`, and on each iteration adds any newly-encountered item to both the tracking set and the output list. The final `out` contains each unique item exactly once, in the order it first appeared.

The narration is **correct for the function the model wished was there**. The actual code has the conditional inverted — it appends only when the item *has already been seen*, so the output will be empty for a deduplicated input like `[1, 2, 3]`. The model explained the intended behavior, not the actual code.

## What's Actually True

Line-by-line the code says: for each item, if it is *already in* `seen`, then *add it to seen* (no-op) and append it. A non-seen item is skipped. The output is the subset of items that appeared at least twice, minus one occurrence. Completely different from deduplication.

## Why This Happens

- The model recognized the function name and setup as the "dedupe with order preservation" pattern, then produced the canonical narration for that pattern without actually reading the control flow.
- Claude in particular tends toward narrating the *intent* inferred from names and structure rather than the behavior of the literal code.
- The narration is fluent and specific — exactly the surface features that make it pass review.

## Detection Strategy

1. **Read the code yourself before reading the explanation.** Use the explanation to confirm your reading, not to replace it.
2. **For any function with non-trivial control flow, require a test case in the explanation.** `dedupe_preserve_order([1, 2, 1])` should produce `[1, 2]`. If the model produces a test case matching *its narration*, you catch the discrepancy when you actually run it.
3. **Look for narration that paraphrases the function's name.** When the explanation tracks the name more closely than the body, suspect name-driven narration.
4. In eval pipelines, always run the code with a test case before accepting the explanation.

## Mitigation Prompt

**Before:**
> Explain what this function does line by line.

**After:**
> Explain what this function does line by line. **For each line, describe the literal effect of the code, not what you think the function is supposed to do.** After the line-by-line explanation, produce one concrete input and compute the output by hand. Flag any discrepancy between the name of the function and what the code actually does.

The "flag any discrepancy" framing converts the failure mode into a feature — instead of narrating intent over implementation, the model is now rewarded for spotting the mismatch.

## Risk Level

🔴 Dangerous.

- Code review is often the last line of defense for correctness. A confidently-wrong explanation makes a reviewer more likely to approve a buggy change.
- In onboarding material, incorrect explanations of real code become the mental model that new engineers internalize. They then write new bugs rhyming with the old misunderstanding.
- For AI-assisted debugging, a wrong explanation sends the user in the wrong direction — hours lost before anyone questions the premise.
