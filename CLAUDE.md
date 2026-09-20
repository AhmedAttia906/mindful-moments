# Project: Life Goals Tracker

## What this is
A personal web app to track and organize goals across six life dimensions
(e.g. health, career, finance, relationships, personal growth, spirituality —
adjust to the actual six areas). Each dimension has its own priority, and
priority can change over time based on the user's current needs — this is
not a static weighting.

## Who it's for
Single user (me). No other accounts, no public sign-up, no multi-tenant
logic needed right now. Don't add authentication complexity beyond a simple
login for me unless I explicitly ask for it.

## Rules that must never be broken
1. **Never delete, overwrite, or run migrations against the database
   without asking me first, in this session, in plain language** — even if
   you believe it's safe or reversible. This includes "cleanup" scripts,
   schema resets, and seed scripts that would touch existing goal data.
2. **Goal and progress data is the most important thing in this project.**
   Treat it as irreplaceable. Before any change that touches how goals or
   progress entries are stored, tell me what could be lost and wait for my
   yes.
3. Don't install new dependencies or packages without telling me what they
   are and why first.
4. Don't change the database schema silently as a side effect of an
   unrelated feature request — call it out explicitly.
5. When done with a task, always show me evidence from the actual running
   app (a screenshot, a URL I can open, or a described click-through) —
   never just tell me "it's done" based on the code alone.
6. Every new feature proposal must say which seams it touches.

## How I want updates delivered
- Plain language, no assumed prior coding knowledge.
- Tell me what changed and why in 2-3 sentences before diving into detail.
- If something risky happened or almost happened, say so clearly, even if
  it was avoided.

## Current state
- Six life dimensions, each with adjustable priority.
- Goals and progress are tracked separately for each dimension.
- Keep this section updated as the project grows, including the tech stack,
  deployed URL, and key screens.
- A task is "Done" only after the code is pushed to the public
  `mindful-moments` repository, GitHub Pages reports a successful deployment,
  and the live page is verified on another device and network with the expected
  content and no stale cache.
