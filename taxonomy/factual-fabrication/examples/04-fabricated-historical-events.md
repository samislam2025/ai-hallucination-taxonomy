# Fabricated Historical Events

**Category:** Factual Fabrication &nbsp;|&nbsp; **Severity:** 🔴 4 &nbsp;|&nbsp; **Frequency:** Occasional
**Models Observed:** GPT-4, GPT-4o, Gemini 1.5 Pro

## The Hallucination

Prompt: *"Write a short history of how the modern restaurant week promotion came to be, with specific events and dates."*

> The modern restaurant week concept traces back to **a 1991 initiative by New York City's hospitality commissioner, Robert Palmieri**, who partnered with roughly 40 mid-tier Manhattan restaurants to launch a fixed-price menu program over a single week in February 1992. Within three years, the model had spread to Chicago and San Francisco.

The broad strokes of NYC Restaurant Week are real (it began in 1992 under a different organizational structure). The specific "hospitality commissioner Robert Palmieri" is invented, as is the February start month and the spread timeline.

## What's Actually True

New York City Restaurant Week was organized by NYC & Company (the convention and visitor bureau) in 1992, initially as a promotion tied to the Democratic National Convention being held in the city. It started in the summer, not February. Other cities adopted similar programs over the following decade, but with different organizers and no direct "spread" from a single initiative.

## Why This Happens

- The model has a rough semantic outline of the event (NYC, 1992, restaurant week) but lacks specific names and dates.
- When asked for specifics, it fabricates plausible ones rather than refusing.
- "Hospitality commissioner" is a real-sounding title that maps to municipal government; the name is generated to match a common Italian-American surname distribution plausible for 1990s New York, which gives the fabrication cultural fluency.

## Detection Strategy

1. **Every named person associated with a historical event must be cross-checked.** Start with a direct Wikipedia or contemporary news search.
2. **Month-level specifics in historical claims are a common failure surface.** The model will often get the year right and the month wrong.
3. **"Spread" or "adoption" timelines are high-fabrication zones.** Verify each city's adoption independently rather than trusting the narrative arc.
4. Look for combinations of a real anchor fact and invented supporting details — this is the signature pattern.

## Mitigation Prompt

**Before:**
> Write a short history of restaurant week with specific events and dates.

**After:**
> Write a short history of restaurant week. Use only events and dates you are highly confident in. Where you are unsure about a specific person, month, or city-by-city detail, use language like "launched in the early 1990s" or "adopted by other major cities over the following decade" rather than inventing names or precise dates. Do not fabricate officials, organizations, or titles.

## Risk Level

🔴 Dangerous.

- Fabricated history is self-reinforcing. Once it appears in a published article, it becomes a citation source for future articles and eventually for retrained models.
- In any content that touches institutional or governmental history, fabricated officials and titles can constitute defamation when they misrepresent real-but-unnamed people, or violate attribution standards when the invented name overlaps with a real person who held the actual role.
- For brands writing "about us" history or industry retrospectives, shipping fabricated history is an embarrassment that invites public correction.
