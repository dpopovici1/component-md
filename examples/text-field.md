# Text Field

A single-line text input for short, free-form user entry. Composes a label, an input control, optional helper text, and an optional error message into a labelled, accessible form control.

---

## 1. API spec

### Properties

| Name | Type | Default | Required | Description |
|---|---|---|---|---|
| `label` | `string` | — | yes | Visible label. Always rendered; never replaced by `placeholder`. |
| `name` | `string` | — | yes | Form control name. Used for submission and as the `for` / `id` linkage. |
| `value` | `string` | `""` | no | Controlled value. |
| `onChange` | `(value: string) => void` | — | no | Fires on every keystroke. Prefer `onBlur` for validation. |
| `onBlur` | `(value: string) => void` | — | no | Fires when focus leaves the input. Default validation hook. |
| `placeholder` | `string` | — | no | Sample input. Disappears on focus. Never used as a label substitute. |
| `helperText` | `string` | — | no | Persistent guidance shown below the input. Replaced by `errorMessage` when `error` is true. |
| `errorMessage` | `string` | — | no | Required when `error` is `true`. Replaces `helperText`. |
| `error` | `boolean` | `false` | no | Toggles the error state. Requires `errorMessage`. |
| `disabled` | `boolean` | `false` | no | Component is not interactive and not submitted. Mutually exclusive with `readOnly`. |
| `readOnly` | `boolean` | `false` | no | Component is not editable but is submitted and focusable. |
| `required` | `boolean` | `false` | no | Component is required for form submission. Renders the required indicator. |
| `leadingContent` | `"icon" \| "text" \| "none"` | `"none"` | no | Content rendered inside the input, before the value. |
| `trailingContent` | `"icon" \| "button" \| "text" \| "none"` | `"none"` | no | Content rendered inside the input, after the value. |
| `size` | `"sm" \| "md" \| "lg"` | `"md"` | no | Density variant. Affects height, padding, and type ramp. |

### Sub-components

- **`<TextField.Label>`** — declared by the parent's `label` prop. Not exposed as a slot.
- **`<TextField.HelperText>`** — declared by `helperText` or `errorMessage`. Not exposed as a slot.
- **`<TextField.LeadingContent>`** — slot. Accepts a single icon (`16px` square) or up to 3 characters of text. Larger content is truncated.
- **`<TextField.TrailingContent>`** — slot. Accepts an icon, a single icon-only button (e.g. clear, password reveal), or up to 3 characters of text.

### Configuration example

```tsx
<TextField
  label="Email address"
  name="email"
  required
  helperText="We'll never share this."
  trailingContent="icon"
  onBlur={(value) => validateEmail(value)}
/>
```

### This component does NOT

- Validate input itself. Validation is the consumer's responsibility, surfaced via `error` and `errorMessage`.
- Mask sensitive content (passwords, card numbers). Use `<PasswordField>` and `<MaskedField>` — siblings.
- Accept multi-line input. Use `<TextArea>`.
- Render a clear button by default. Pass `trailingContent="button"` and supply the button.
- Replace the label with the placeholder. The label is always visible.
- Resize horizontally based on content. Width is set by the parent layout.

---

## 2. Structural spec

### Interactivity

Interactive in all states except `disabled` and `read-only` (focusable but not editable in `read-only`; not focusable in `disabled`).

### Dimensions

| Element | `size="sm"` | `size="md"` | `size="lg"` |
|---|---|---|---|
| Input height | `--space-8` (32px) | `--space-10` (40px) | `--space-12` (48px) |
| Horizontal padding | `--space-3` (12px) | `--space-3` (12px) | `--space-4` (16px) |
| Gap (icon ↔ value) | `--space-2` (8px) | `--space-2` (8px) | `--space-3` (12px) |
| Border radius | `--radius-sm` | `--radius-md` | `--radius-md` |
| Border width | `--border-width-1` (1px) | `--border-width-1` (1px) | `--border-width-1` (1px) |
| Type ramp | `--font-body-sm` | `--font-body-md` | `--font-body-lg` |

Vertical rhythm: label → `--space-1` → input → `--space-2` → helper / error.

### Layout direction and overflow

- LTR by default. Mirrors in RTL: `leadingContent` swaps with `trailingContent`, padding is logical (`padding-inline-start` / `padding-inline-end`), the cursor and selection follow the locale direction.
- Value overflow is horizontal scroll within the input — never wrap.
- Helper / error text wraps to a maximum of 3 lines, then truncates with ellipsis.

### Layer-tree-to-API translation

| Figma layer | Engineering API |
|---|---|
| `leadingContent: { type: "icon" }` | `leadingContent: "icon"` |
| `leadingContent: { type: "text" }` | `leadingContent: "text"` |
| Layer hidden / removed | `leadingContent: "none"` |
| `state: error + message: visible` | `error: true, errorMessage: string` |
| `state: error + message: hidden` | Invalid configuration — `errorMessage` is required when `error` is true |
| `density variant: compact` | `size: "sm"` |
| `density variant: default` | `size: "md"` |
| `density variant: comfortable` | `size: "lg"` |

### Responsive

The component does not change layout across breakpoints. Width is governed by the parent layout container.

---

## 3. Color assignment

All values are role tokens. Hex is never used. Dark mode falls out of token swapping for default, hover, focus-visible, pressed, and read-only states. The `error` state is declared explicitly in dark mode — see the dark-mode note at the end of this slice.

### State: `default`

| Element | Property | Token | Notes |
|---|---|---|---|
| Label | color | `--color-text-secondary` | |
| Required indicator | color | `--color-text-critical` | |
| Input border | border-color | `--color-border-default` | |
| Input background | background-color | `--color-surface-default` | |
| Input value | color | `--color-text-primary` | |
| Placeholder | color | `--color-text-tertiary` | |
| Leading / trailing icon | color | `--color-icon-secondary` | |
| Helper text | color | `--color-text-secondary` | |

### State: `hover`

| Element | Property | Token | Notes |
|---|---|---|---|
| Input border | border-color | `--color-border-strong` | |

All other elements inherit `default`.

### State: `focus-visible`

| Element | Property | Token | Notes |
|---|---|---|---|
| Input border | border-color | `--color-border-focus` | |
| Focus ring | outline-color | `--color-border-focus` | 2px outline, 2px offset |
| Focus ring | outline-style | solid | |

### State: `disabled`

| Element | Property | Token | Notes |
|---|---|---|---|
| Label | color | `--color-text-disabled` | |
| Input border | border-color | `--color-border-disabled` | |
| Input background | background-color | `--color-surface-disabled` | |
| Input value | color | `--color-text-disabled` | |
| Leading / trailing icon | color | `--color-icon-disabled` | |
| Helper text | color | `--color-text-disabled` | |

### State: `read-only`

| Element | Property | Token | Notes |
|---|---|---|---|
| Input border | border-color | `--color-border-subtle` | |
| Input background | background-color | `--color-surface-subtle` | |
| Input value | color | `--color-text-primary` | Value remains primary — content is the focus |
| Leading / trailing icon | color | `--color-icon-secondary` | |

### State: `error`

| Element | Property | Token | Notes |
|---|---|---|---|
| Input border | border-color | `--color-border-critical` | |
| Input background | background-color | `--color-surface-default` | Background does **not** tint — error is signalled by border + message + icon |
| Trailing icon (error indicator) | color | `--color-icon-critical` | Icon rendered automatically when `error` is true, regardless of `trailingContent` |
| Error message | color | `--color-text-critical` | Replaces helper text |
| Focus ring (when also focused) | outline-color | `--color-border-focus` | Focus ring takes priority over error border on focus-visible |

### Dark mode note

All role tokens above resolve to dark-mode equivalents automatically. The `error` state is verified in dark mode against WCAG 2.2 AA contrast for both border and message — `--color-border-critical` is desaturated in dark mode to avoid vibration against `--color-surface-default`.

---

## 4. Voice / screen-reader spec

### Focus order

1. Input control. Single tab stop.
2. If `trailingContent="button"`, the trailing button receives focus *after* the input. Tab moves into the button; Shift+Tab returns to the input; Escape returns focus to the input from inside the button.
3. Leading content is never a tab stop. Decorative icons are `aria-hidden="true"`.

Keyboard:

- `Tab` / `Shift+Tab` — move focus.
- `Enter` — submits the parent form (default browser behaviour).
- `Escape` — clears focus from the input. Does not clear the value.
- All character keys, arrow keys, `Home`, `End`, `Backspace`, `Delete` behave per native input.

### State: `default`

| Element | Role | Name | State attributes | Announcement |
|---|---|---|---|---|
| Input | `textbox` | `label` value | `aria-required` if required | "[label], [required if true], edit text" |
| Helper text | — | — | linked via `aria-describedby` | Read after the label on focus |
| Leading icon | — | — | `aria-hidden="true"` | Not announced |

### State: `focus-visible`

Same as `default`. Focus is communicated visually via the ring and to screen readers via the focus event itself — no additional ARIA needed.

### State: `disabled`

| Element | Role | Name | State attributes | Announcement |
|---|---|---|---|---|
| Input | `textbox` | `label` value | `aria-disabled="true"` | "[label], dimmed, edit text" — VoiceOver. "[label], disabled" — TalkBack. |

Use `aria-disabled` rather than the `disabled` attribute when the field must remain in the tab order for context. Use the native `disabled` attribute when it should not.

### State: `read-only`

| Element | Role | Name | State attributes | Announcement |
|---|---|---|---|---|
| Input | `textbox` | `label` value | `aria-readonly="true"` | "[label], read-only, [value]" |

The value is announced because read-only fields exist to surface content, not to collect input.

### State: `error`

| Element | Role | Name | State attributes | Announcement |
|---|---|---|---|---|
| Input | `textbox` | `label` value | `aria-invalid="true"`, `aria-describedby` linked to error message | "[label], invalid entry, [error message], edit text" |
| Error message | — | — | linked via `aria-describedby` | Read after the input's name on focus |
| Live region (on error trigger) | `status` | — | `aria-live="polite"`, `aria-atomic="true"` | Announces "[label]: [error message]" when the error first appears |

The live region announcement fires once when the error first appears (on blur or submit), not on every keystroke. When the user re-enters the field and corrects the value, `aria-invalid` is removed on the next blur — no announcement.

### Platform differences

- **VoiceOver (iOS / macOS)** announces the label, then the value, then state, then hint (helper or error) — in that order. Hints fire after a short delay.
- **TalkBack (Android)** announces the label first, then state, then hint, then value. Live regions fire immediately, not on delay.
- **NVDA / JAWS (Windows)** mirror VoiceOver order for forms but read `aria-describedby` content in full immediately.
- **Web-only.** Native iOS / Android equivalents (UITextField, EditText) follow platform conventions and are not specified here.

---

## 5. Behavioral spec

### Trigger and dismiss

The component is always rendered when present in the form. It does not appear or dismiss conditionally — that is the parent's responsibility.

### Validation timing

- **`onChange`** does not trigger validation. It updates the controlled value only.
- **`onBlur`** is the default validation hook. The consumer calls `onBlur`, runs validation, and sets `error` + `errorMessage` if the value is invalid.
- **On form submit**, the parent re-validates all fields. Fields that were not blurred enter the error state at submission.
- **Error clearing.** Once a field is in error, the error clears on the next `onBlur` *only* if validation passes. It does not clear on `onChange` — clearing on every keystroke causes flicker.

### Empty state

The default state when `value === ""` is the empty state. Placeholder text (if provided) is shown. There is no separate "empty" visual treatment — the input is the input.

### Loading state

Not specified at the field level. Forms that load asynchronously should disable the field with `disabled={true}` and `helperText="Loading…"` until ready, or use `<TextField.Skeleton>` (separate component, not in scope here).

### Edge cases

| Case | Behaviour |
|---|---|
| `disabled` and `error` both true | `disabled` wins. Error styling and `aria-invalid` are suppressed. The field is not submitted, so its validity does not matter. |
| `readOnly` and `error` both true | `error` wins. Read-only fields can be flagged invalid in review-style flows. |
| `disabled` and `readOnly` both true | `disabled` wins. Logged as a developer warning in dev builds. |
| Value exceeds visible width | Horizontal scroll within the input. Cursor follows. Selection follows. No wrap. |
| `error: true` with no `errorMessage` | Logged as a developer error in dev builds. Visual error state renders; ARIA error contract does not. |
| Pasting a value longer than `maxLength` | Truncate to `maxLength`. Fire `onChange` with the truncated value. Announce truncation only if `aria-live` region is configured by the parent. |
| User is offline during async validation | The `error` state is the consumer's call. The component does not surface network state. |

### Conflict resolution priority

`disabled` > `readOnly` > `error` > `default`.

---

## Motion (optional)

| Transition | Duration | Easing | Notes |
|---|---|---|---|
| Border colour on hover / focus / error | `--motion-duration-fast` (120ms) | `--motion-easing-standard` | |
| Error message reveal | `--motion-duration-fast` (120ms) | `--motion-easing-emphasized-decelerate` | Fade + 4px rise. Skipped under `prefers-reduced-motion`. |
| Focus ring | none | none | Instant. Focus indicators must not animate (WCAG 2.2). |

---

## Counter-examples (optional)

- **Do not use** `<TextField>` for a search input with autocomplete. Use `<Combobox>`.
- **Do not use** `<TextField>` for a value picked from a fixed list (country, currency). Use `<Select>` or `<Combobox>`.
- **Do not use** `<TextField>` for inline-edit-on-click patterns. Use `<EditableText>`.
- **Do not use** `placeholder` as a label substitute. Placeholder disappears on focus and is invisible to assistive tech as a label.
- **Do not** render the error state without an `errorMessage`. The colour change alone is not an accessible error signal.

---

## Localization (optional)

- **RTL.** `leadingContent` and `trailingContent` swap visually. The cursor, selection, and value direction follow the locale. The value direction can be forced LTR for fields that always carry LTR content (URLs, email addresses) by setting `dir="ltr"` on the input — declared by the consumer, not automatic.
- **Numerals.** When the locale is Arabic (`ar-*`) or Persian (`fa-*`) and the value is numeric, the consumer chooses Western or Eastern Arabic-Indic digits via CSS `font-variant-numeric` or by formatting the controlled value. The component itself does not transform digits.
- **String expansion.** Reserve +30% width for German label translations and +40% for Finnish. Do not constrain the label to a single line.
- **Date / time / decimal entry.** Use `<DateField>`, `<TimeField>`, `<NumberField>` — siblings. `<TextField>` does not handle locale-conditional formatting.
