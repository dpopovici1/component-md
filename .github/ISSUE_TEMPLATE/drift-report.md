---
name: Production-drift report
about: A `component-md` spec didn't capture something an LLM agent then got wrong in production
title: "<component>: <one-sentence problem>"
labels: drift-report
---

## Component

What component were you spec'ing? (Anonymise the design system if needed — "a fintech B2B billing select" is fine.)

## Stack

Which AI tool consumed the spec? Claude Code / Figma Make / v0 / Cursor / Subframe / Magic Patterns / Storybook MCP / other?

## What the spec said

Paste the relevant slice (or excerpt). Anonymise tokens if needed.

## What the agent generated

Paste the diff, the rendered output, or describe the drift.

## What was missing

Which slice / field / state would have prevented this? If the schema would need a new section to capture it, name it.

## Severity

- [ ] Cosmetic — visual only, fixable in review
- [ ] Functional — wrong behaviour, but caught in QA
- [ ] Shipped — reached production. Fabricated tokens. Unstyled HTML. Broken ARIA.
