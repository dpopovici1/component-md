# Contributing to `component-md`

This format gets better the more components stress-test it. Critique, anonymised production-drift reports, and PRs that extend the spec are all welcome.

## What's most useful

In rough order of value:

1. **Production-drift reports.** "We tried `component-md` for our Combobox at [company]. Here's what the spec couldn't capture: …" Anonymise as needed. These are the issues that move the schema.
2. **Worked examples for hard components.** Modal, combobox, data table, date range picker, file upload, toast. Open a PR adding `examples/<name>.md`. The schema reveals its weak spots fastest on these.
3. **Schema PRs.** Add an optional slice, refine a required one, fix an anti-pattern. Argue for it in the PR description against a real component that needed it.
4. **Tooling.** Linters, validators, MCP servers, Figma-export-to-`component-md` scripts. These should live in their own repos and link back here.

## What's less useful right now

- Bikeshedding section ordering. The five-slice contract is opinionated on purpose. Argue against it with a component that broke on it, not with a preference.
- Renaming things. The names are sticky now. They will change at 1.0, deliberately.
- Adding required slices. Required slices are a tax on every spec author. The bar to add one is a component class that systematically fails without it.

## How to open a useful issue

Title pattern: `<component class>: <one-sentence problem>`.

In the body:

1. The component you were spec'ing.
2. The slice that failed (or the slice that should exist and doesn't).
3. The exact field / table / state that broke.
4. What you wrote instead, and what you'd recommend the schema do.

Issues without those four are hard to action.

## How to open a useful PR

For schema changes — open the issue first. Don't surprise the maintainer with a 600-line schema PR.

For example components — go ahead. PR a complete `.md` file in `examples/`, named in `kebab-case`. Run the reconciliation checklist in `SPEC.md` before pushing. The PR description should call out which slices were hardest and why.

For typo / clarity fixes — go ahead. No issue needed.

## Tone

Direct. Component specs are technical contracts; the discussion around them should be too. Credit prior art (Nathan Curtis's Specs, Ian Guisard's uSpec, Diana Wolosin's Indeed benchmarks) when it applies. Disagree freely; assume good faith.
