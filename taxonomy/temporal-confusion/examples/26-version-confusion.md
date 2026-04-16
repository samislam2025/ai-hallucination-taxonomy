# Version Confusion

**Category:** Temporal Confusion &nbsp;|&nbsp; **Severity:** 🔴 4 &nbsp;|&nbsp; **Frequency:** Common
**Models Observed:** All three

## The Hallucination

Prompt: *"Write me a React component that uses the `useEffect` hook to fetch data on mount. Use the current idiomatic pattern."*

```jsx
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(setData);
  }, [userId]);

  return data ? <div>{data.name}</div> : <div>Loading...</div>;
}
```

> This is the standard pattern for fetching data on mount in React.

For a React 16.8 or 17 codebase this is fine. For a React 19 / Next.js App Router project in 2026, this is not the current idiom. Server components, `use` with a promise, or framework-provided data-fetching (`fetch` inside a server component with caching) would be more appropriate. The model produced the well-documented pre-Suspense pattern and called it current.

## What's Actually True

The idiom has shifted. In a server-component-enabled app, data fetching happens on the server, not in a `useEffect` client-side. `useEffect` for data fetching is still valid for client-only components but is no longer the "current idiomatic pattern" for new code. A correct answer either asks about the project's React version and framework, or presents the pattern with a clear "for client-side fetching" qualifier.

## Why This Happens

- **Old patterns dominate the training corpus.** Years of tutorials showed exactly this `useEffect`+`fetch` shape. It has huge representation.
- **Model has no version context unless told.** Without a stated React version, the model defaults to what was most common across its training data.
- **"Idiomatic" is a loaded word the model ignores temporally.** It treats "idiomatic" as meaning "recognizable" rather than "current best practice."

## Detection Strategy

1. **Always state the framework + version in prompts for framework code.** If you do not, assume the output targets the training-median version, which is often 1–2 years behind.
2. **Compare the output's imports and API surface to your actual installed version.** Deprecated APIs are a version-confusion signal.
3. **Watch for version-agnostic language on version-sensitive topics.** "The current idiom" without a version anchor is a red flag.
4. In eval, test the model with multiple framework versions and grade whether the output's pattern actually matches that version's recommended approach.

## Mitigation Prompt

**Before:**
> Write me a React component that uses `useEffect` to fetch data on mount. Use the current idiomatic pattern.

**After:**
> Write me a data-fetching React component for a **Next.js 15 App Router project with React 19**. Use the pattern that is idiomatic for *this specific version*. If there is a server-component approach that would be preferred for this use case, use that instead and explain why. State any version-specific features you are using.

Key moves:
- Name the framework + version explicitly.
- Invite the model to choose a different pattern if it fits the version better than what you asked for.
- Force an explicit statement of version-specific features, which is a self-audit.

## Risk Level

🔴 Dangerous.

- Code written in deprecated patterns survives in codebases for years and is expensive to migrate.
- Engineers who learn from AI-generated tutorials absorb the outdated pattern as "the way it is done."
- Security: deprecated patterns sometimes have known issues that newer patterns avoid (e.g., fetch-in-effect creates race conditions that server components sidestep).
- Interviewing candidates or reviewing code produced with AI: inability to recognize version-appropriate idioms becomes a team-wide skills gap.
