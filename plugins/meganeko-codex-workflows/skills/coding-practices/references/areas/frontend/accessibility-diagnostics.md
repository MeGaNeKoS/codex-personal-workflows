# Frontend Accessibility Diagnostics

Use this before committing UI changes that add or modify interactive controls, color semantics, forms, navigation, dialogs, tables, or application shell structure.

## 1. Check Semantics First

Prefer native HTML controls and landmarks before adding ARIA.

Verify:
- buttons, links, inputs, selects, textareas, tables, headings, lists, and landmarks use the correct native element
- icon-only controls have an accessible name
- visible labels and accessible names do not contradict each other
- decorative icons are hidden from assistive technology
- ARIA is not used to fake behavior that native elements already provide

## 2. Verify Keyboard Behavior

Every user action must be reachable and understandable with a keyboard.

Check:
- tab order follows the visual and workflow order
- focus is visible in default, hover, active, disabled, collapsed, expanded, and error states
- focus is not trapped unless a modal/dialog intentionally owns it
- Enter and Space work for button-like controls
- route changes, dialogs, drawers, menus, and popovers place focus intentionally

## 3. Measure Contrast And Color Use

Do not rely on visual judgment for contrast.

Measure text and icon contrast against the actual computed background in normal, hover, focus, active, selected, disabled, and error states. Treat translucent backgrounds, inherited colors, and nested surfaces as computed values, not design assumptions.

Use color as reinforcement, not the only signal. Pair semantic color with text, icon shape, label, position, or state.

## 4. Check Target Size And Spacing

Interactive targets should be large enough to use reliably.

Prefer at least `44px` by `44px` for primary app shell controls, icon buttons, mobile controls, and repeated action targets. If a dense desktop UI uses smaller visual artwork, keep the hit target large and center the artwork inside it.

Verify target size, not only SVG or text size.

## 5. Inspect States

Accessibility bugs often appear outside the default state.

Check:
- hover
- focus-visible
- active/pressed
- selected/current
- disabled
- loading
- error/invalid
- collapsed/expanded
- desktop/tablet/mobile breakpoints
- reduced motion

## 6. Audit With Browser Measurements

When browser tooling is available, inspect computed DOM state.

Measure:
- accessible names for interactive controls
- focus outline style and offset
- contrast ratio from computed foreground/background colors
- target width and height
- landmark labels
- form label/error associations
- whether hidden content is still focusable

Do not accept "looks fine" when the issue can be measured.

## 7. State The Result

Before committing UI work, state the accessibility checks that passed and any remaining risk.

Good:
`Logout is not color-only: it has text, icon, aria-label, 7.82:1 hover contrast, and a visible focus outline.`

Good:
`The icon artwork is 20px, but the button hit target is 44px and the icon is centered in it.`

Bad:
`A11y looks okay.`
