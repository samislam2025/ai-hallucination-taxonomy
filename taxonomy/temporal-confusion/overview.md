# Temporal Confusion

> The model's sense of time is wrong — information is dated, events are placed on the wrong side of "now," versions are confused.

Temporal confusion is an almost-inevitable consequence of how LLMs are built. The model has a training cutoff, but no reliable internal model of *when the conversation is happening*. That gap produces three recurring failures: stale facts presented as current, scheduled or recent events described in the wrong tense, and version confusion in fast-moving libraries and products.

## Why this happens

- **Hard training cutoff.** The model stops ingesting data at a specific date. Anything after that is invisible to it unless retrieved at inference time.
- **No internal clock.** The model cannot know what today is without being told. Generations assume an approximate "present" that drifts based on the prompt's cues.
- **Long tail of historical data.** Version-specific documentation about old releases weighs heavily in training; newer versions are underrepresented until the next retraining cycle.
- **Tense-priors from training.** Events the model saw described in past tense get generated in past tense, even if they were future-tense at training time.

## Where this hurts most

- **Software development.** API surfaces, framework idioms, and library defaults shift faster than retraining cycles. A model confidently using a 2-major-versions-old pattern is a common failure.
- **Current-events questions.** "Who is the current CEO of X?" gets answered with cutoff-era data.
- **Scheduled-events questions.** "When is the next election in country Y?" gets an answer as though it has already happened.
- **Product recommendations.** Models recommend deprecated or discontinued products with high confidence.

## Model-specific tendencies

- **All three models** do this. The specific failure depends on cutoff date and retrieval setup.
- **Claude** is typically more willing to flag uncertainty about recent events than GPT or Gemini.
- **Gemini** with live search integration is the least prone but inherits retrieval errors when search misfires.

## Detection signals

- Any claim about a company, product, framework version, or public figure that could have changed in the last 12–24 months.
- Future-tensed events described in past tense, or vice versa.
- Version-specific code examples with no version number stated.
- "As of 2023..." in an output where the user never supplied a date.

## Examples

24. [Outdated info stated as current](examples/24-outdated-info-stated-as-current.md)
25. [Future events as past](examples/25-future-events-as-past.md)
26. [Version confusion](examples/26-version-confusion.md)
