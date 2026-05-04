# component-md

**A markdown spec format for design system components — readable by humans, queryable by LLMs.**

One file per component. Five required slices: API, structure, color, voice, behavior. Platform-agnostic. Lives in your repo, your docs, or your Figma export pipeline. The Figma file goes back to being a studio. The `.md` is the blueprint.

---

## Why this exists

A Figma file is a source of *information*, not a source of *truth*. When an LLM (Claude Code, Figma Make, v0, Cursor, Claude Design) reads a Figma file directly, it gets layout, colours, and the layer tree — and guesses everything else: which token governs which state, focus order, ARIA announcements, dismiss rules, error behaviour. The output is *close* but never *right*, and fixing the gap costs more than building did.

In April 2026, Kaelig Deloumeau-Prigent published a public retrospective of an 8-agent pipeline running inside the Intuit Design System team. Version 0.2 of the pipeline produced a button component using 27 fabricated CSS tokens — every variable used the correct `--ids-*` prefix. None existed. Tests passed. The component shipped to staging as unstyled HTML. (Read it: [kaelig.fr/design-system-components-with-ai-agent-teams](https://www.kaelig.fr/design-system-components-with-ai-agent-teams/).)

That failure mode is not the agent's fault. It's the design system's. If the system isn't legible to an LLM, the agent will guess plausibly — and the part that ships is the guess.

`component-md` is the artifact that closes the gap at the component level.

---

## Prior art

This repo would not exist without two people:

- **Nathan Curtis — [Specs](https://specs.eightshapes.com/)** (EightShapes Figma plugin). Specs makes design *tokens* legible to AI agents — structured YAML token references embedded in component frames. Kaelig's pipeline names it the most reliable token source it ever found. `component-md` is the component-layer companion to what Specs does at the token layer. Tokens without component context produce styled-but-wrong UI; component context without tokens produces unstyled UI. You need both.

- **Ian Guisard — [uSpec / Component.md](https://uspec.design)** ([github.com/redongreen/uSpec](https://github.com/redongreen/uSpec)). The five-slice schema and the extraction-then-reasoning workflow are based directly on Ian's Component.md methodology, published April 2026. If you want the canonical Figma plugin and the original spec, go there. This repo is an opinionated, deployment-ready distillation aimed at design system teams shipping AI handoff in production — not a replacement.

Adjacent context:

- **Diana Wolosin (Indeed)** is benchmarking 8 metadata configurations to measure which structures actually let LLMs reason about a component. Her work is the empirical layer underneath this format.
- **Brad Frost — Atomic Design** is the precedent for how a methodology becomes a category. `component-md` is not a tool. It's a contract.

---

## The schema

Every `component.md` covers five slices. The schema is the contract — what fills each slice flexes to the component, but all five must be present.

| Slice | What it specifies |
|---|---|
| **1. API spec** | Properties, sub-components, configuration examples, what the component is and is not |
| **2. Structural spec** | Dimensions, spacing, density / size / shape variants, layer-tree-to-API translation |
| **3. Color assignment** | The role token (not the hex) governing each element in each state |
| **4. Voice / screen-reader spec** | Focus order, ARIA roles, announcements, VoiceOver / TalkBack differences |
| **5. Behavioral spec** | Dismiss rules, error / empty / loading states, edge cases, validation timing |

Optional, add when warranted:

- **Motion** — durations, easings, what animates and what does not
- **Decision tree** — when to use which variant
- **Counter-examples** — how the component should *not* be used
- **Migration notes** — diffs from the previous spec version
- **Localization** — RTL, Arabic-Indic numerals, string expansion, locale-conditional behaviour

Read [`SPEC.md`](./SPEC.md) for the field-level contract.

---

## The killer principle

> The colour spec and the screen-reader spec for the same state must sit in the same file, owned by the same component, shipped together.

This is what separates `component-md` from a Storybook page or a token export. Storybook pairs colour with code. Token exports pair colour with structure. Neither pairs colour with voice. When error styling is added without ARIA, or `aria-invalid` is added without the matching error colour token, the component ships half-broken — sighted users see the error, screen-reader users don't (or the inverse). The format forces the pairing because the file forces the pairing.

See [`examples/text-field.md`](./examples/text-field.md) for what this looks like in practice. The error-state section is the part to read first.

---

## How to use

**As a designer:** spec a component once, push the `.md` to your design system repo (or Notion, or Storybook MDX, or wherever your team reads docs). Designers query it the same way engineers do. No more "ask the design system team in Slack."

**As an engineer using AI tools:** before you prompt Claude Code / Figma Make / v0 / Cursor, paste the component's `.md` into context. Or wire it via MCP. The agent stops inventing components that don't exist and stops fabricating tokens that don't exist, because the contract is in front of it.

**As a design system team:** treat the `.md` as the source of truth. Figma is the studio for cheap iteration. The `.md` is what ships and what gets read. When they disagree, the `.md` wins — or the `.md` is updated, deliberately.

The workflow is two-stage:

1. **Extraction** (cheap, scripted) — pull raw data from Figma. Use Ian's [uSpec plugin](https://uspec.design), the Figma MCP server, a JSON export, or a screenshot + token list. Capture component name, variants, layer tree, token names (not hex), sub-component instances, file inconsistencies.
2. **Reasoning** (expensive, agentic) — turn extracted data into the five slices. Four passes: structure → color → voice → behavior, then reconcile. Each pass owns one slice. Reconciliation looks for gaps (state in colour table missing from ARIA, hex values that should be tokens, "TBD"s) and re-runs the owning pass.

> Extraction without reasoning is a data dump. Reasoning without extraction is a hallucination.

---

## Status

This is `v0.1`. The schema is opinionated but not frozen. The example is one component. Critique welcome — especially production-drift reports from teams using this with Claude Code, Figma Make, Cursor, or Subframe.

If you ship `component.md` files at your company, open an issue or PR with anonymised excerpts. The format gets better the more components stress-test it.

---

## License

MIT. See [`LICENSE`](./LICENSE).

---

## Maintainer

Built and maintained alongside design system audits for healthcare and fintech SaaS. If your design system isn't legible to your AI tools yet, the audit is the upstream layer — the `.md` is the downstream artifact. [Get in touch](https://github.com/) for audits and rollouts.
