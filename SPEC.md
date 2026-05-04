# `component-md` — Specification

**Version:** 0.1
**Status:** Opinionated, not frozen. Breaking changes possible until 1.0.

---

## File conventions

- One file per component. Filename is the component name in `kebab-case`: `text-field.md`, `stacked-button-group.md`, `date-picker.md`.
- UTF-8, LF line endings, max 120-char lines (soft).
- The file MUST start with a level-1 heading naming the component, followed by a one-sentence purpose statement.
- The five required slices appear in fixed order: API → Structure → Color → Voice → Behavior.
- Optional slices follow the required five, in any order.
- Tables are the preferred format for state × element pairings. They read cleanly for humans and parse cleanly for LLMs.

---

## Required slice 1 — API spec

**Purpose:** define what the component *is*, what it accepts, and what it does not do.

Must include:

- **Properties table.** Columns: `name`, `type`, `default`, `required`, `description`. Types use TypeScript-style unions (`"primary" | "secondary"`), not free text.
- **Sub-components.** Each named sub-component declared explicitly, with the same properties contract. Composition is not implicit.
- **One configuration example.** Minimum viable invocation, in pseudocode or JSX.
- **Negative space.** A "this component does NOT" list. At least three items. Forces the author to draw the boundary.

Anti-patterns:

- Free-text prop descriptions like "use this for primary actions." Use enums.
- Sub-components defined inline inside another component's prop description. Promote them to siblings.
- Skipping the negative-space list because "it's obvious." It isn't.

---

## Required slice 2 — Structural spec

**Purpose:** translate the layer tree from Figma into an engineering model.

Must include:

- **Dimensions per density / size variant.** Width, height, padding, gap, border-radius, border-width. Use design tokens (`--space-2`, `--radius-md`) — not pixel values — wherever the system supports them.
- **Layout direction and wrapping behavior.** Specify what happens when content overflows.
- **Layer-tree-to-API translation.** This is the load-bearing field. Figma uses structural patterns (`leadingContent: true, subcomponent: icon`); engineering uses unions (`leadingContent: "icon" | "image" | "text" | "none"`). The translation belongs in this slice.
- **Interactivity flag.** Is this component interactive? Non-interactive components (badge, pill, tag, chip-as-label) skip hover / focus / pressed tables in slices 3 and 4. Decide here.

Anti-patterns:

- Pixel values when the design system has tokens for the same dimensions.
- Implicit responsive behavior. State the breakpoints.
- Specifying density variants as separate components. They are one component with a `density` prop.

---

## Required slice 3 — Color assignment

**Purpose:** assign the role token (not the hex) governing each element in each state.

Must include:

- **One table per state.** States typically include: `default`, `hover`, `focus-visible`, `pressed`, `disabled`, `read-only`, `error`, `success` — whichever apply.
- **Columns:** `element`, `property`, `token`, `notes`.
- **Tokens, not hex.** If a hex appears in this slice, the spec is incomplete. Trace the hex to the role token that owns it. If no role token exists, file an issue against the token system — that is the actual finding.
- **Dark mode and high-contrast mode** declared explicitly when the system supports them. Do not assume dark mode falls out of token swapping.

Anti-patterns:

- Mixing primitive tokens (`--color-blue-500`) with role tokens (`--color-action-primary`). Use role tokens. Primitives are an implementation detail.
- A single "all states" table. The point of separate tables is that the reader can pair each state's colour with its voice contract in slice 4.
- "Same as default" for a state. Write it out. Repetition is cheap; ambiguity is expensive.

---

## Required slice 4 — Voice / screen-reader spec

**Purpose:** specify the ARIA / VoiceOver / TalkBack contract for every state in slice 3.

Must include:

- **One table per state, mirroring slice 3.** This pairing is the killer principle. State headings in slices 3 and 4 must match exactly.
- **Columns:** `element`, `role`, `name`, `state attributes`, `announcement`.
- **Focus order.** Numbered list of focus stops within the component. Modifier keys (`Tab`, `Shift+Tab`, `Enter`, `Escape`, `Space`, arrow keys) and their behaviours.
- **Platform differences.** VoiceOver and TalkBack differ on live-region behaviour, hint announcement, and grouping. Spec both, or declare web-only.
- **Live regions.** If the component announces dynamic content, specify `aria-live` politeness, the trigger, and the announcement string template.

Anti-patterns:

- "Use semantic HTML" with no further detail. Specify the elements.
- Copying ARIA verbatim from MDN without applying it to *this* component's states.
- Skipping platform differences because "we only ship web." Declare it explicitly.

---

## Required slice 5 — Behavioral spec

**Purpose:** specify the rules that govern the component's lifecycle and edge cases.

Must include:

- **Trigger and dismiss rules.** When does the component appear, persist, and disappear?
- **Error firing logic.** When does validation run — on change, on blur, on submit, debounced? When does the error state clear?
- **Empty, loading, and edge states.** What renders when there is no data, when data is loading, when input exceeds the maximum length, when the user is offline?
- **Conflict resolution.** What happens when two contradictory props are passed (e.g., `disabled` and `error` together)?

Anti-patterns:

- Spec'ing happy-path only. The bug surface is in the edge states.
- Validation timing left to the implementer. It is a UX decision, not an engineering one.
- Empty state described as "show empty state" with no copy, no illustration token, no CTA.

---

## Optional slices

Add when the component warrants them. Convention: slice heading uses the same level as required slices.

- **Motion** — durations (token, e.g., `--motion-duration-fast`), easings (token, e.g., `--motion-easing-emphasized`), what animates, what stays static, reduced-motion fallback.
- **Decision tree** — flowchart or numbered conditional ("If the action is destructive AND non-reversible, use Dialog over Toast"). Useful when the component competes with siblings for the same job.
- **Counter-examples** — explicit "do not use this for X" with one-sentence reason. Reduces misuse.
- **Migration notes** — diffs from the previous spec version. Critical when the spec ships alongside a breaking change in the implementation.
- **Localization** — RTL mirroring rules, Arabic-Indic / Eastern Arabic numeral handling, string-expansion budget (German: +30%, Finnish: +40%), locale-conditional behaviours (date format, decimal separator, week-start day).

---

## Reconciliation checklist

After all five slices are written, read the file as one document and verify:

- [ ] Every state in slice 3 has a matching state in slice 4. Same heading text.
- [ ] No hex values appear in slice 3.
- [ ] Every sub-component declared in slice 1 has structure, color, voice, and behavior coverage.
- [ ] No "TBD", "?", or "see Figma" in any slice. If you cannot resolve it, write the question explicitly under an `## Open questions` heading at the end of the file.
- [ ] The negative-space list in slice 1 is honoured by the rest of the spec — none of the listed "this component does NOT" items show up implicitly later.
- [ ] If slice 2 declares the component non-interactive, slices 3 and 4 do not contain hover / focus / pressed tables.
- [ ] Focus order in slice 4 matches the layer tree implied by slice 2.

If any check fails, re-run the owning slice. Reconciliation is part of the workflow, not an afterthought.

---

## What this spec is not

- Not a token system. Use [DTCG](https://design-tokens.github.io/community-group/format/), Specs, or your own.
- Not a component implementation. Use React, Vue, Web Components, SwiftUI — whichever your platform demands. The `.md` is platform-agnostic.
- Not a Storybook replacement. Storybook visualises. `component-md` contracts. Pair them.
- Not an accessibility audit. The voice slice is the *intended* contract. The audit is the *measured* one.
