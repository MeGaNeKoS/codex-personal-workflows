# Frontend Layout Diagnostics

Use this before changing layout code when the user reports visual weirdness or a layout problem.

## 1. Locate The Problem

Identify the exact surface and state before editing.

Determine:
- which UI region is involved
- which interaction state is involved
- whether the issue is structure, alignment, spacing, sizing, overflow, visual weight, or responsive behavior

If the surface or intended layout is unclear, ask a short clarification before changing structure.

## 2. Preserve Layout Intent

Do not treat a layout bug as permission to redesign the screen.

Preserve the intended layout pattern unless the user explicitly asks to change it. Fix the failing part inside the existing pattern.

Do not add fake content to hide a structural problem.

## 3. Measure The Relevant Boxes

When DOM/browser inspection is available, measure before editing.

Inspect:
- parent container
- visible element
- hover/focus target
- icon or text inside the target
- hidden/collapsed child
- scroll or clipping container

For alignment issues, compare centers numerically instead of relying only on screenshots or visual intuition.

## 4. Check Hidden Layout Participation

For collapsed, animated, or visually hidden UI, verify hidden children are not still affecting layout.

Check whether hidden children still consume:
- width or height
- grid/flex tracks
- gap
- margins
- line height

For visually hidden accessibility text (`sr-only`, clipped labels, hidden captions), verify document scroll size and the hidden element's bounding box. If hidden text causes overflow, prefer semantic naming (`aria-label`, `aria-labelledby`) or a visible label/caption instead of forcing clipped content into that layout.

For icon-only controls, ensure the icon is centered inside the actual hover/focus target.

## 5. Inspect The Reported State

If the issue appears in hover, focus, active, selected, collapsed, expanded, refreshed, desktop, tablet, or mobile state, inspect that exact state.

Do not fix only the default state when the reported problem is state-specific.

For hover/focus issues, check:
- target size
- target position
- icon/text alignment inside the target
- hover background shape
- focus ring placement
- native tooltip leakage
- whether the state visually implies selection by mistake

## 6. State The Diagnosis

Before editing, state the concrete diagnosis in plain terms.

Good:
`The icon is 7px above the button center because the hidden label still participates in grid layout.`

Good:
`The border is on an inner placeholder, but the shell region itself needs the styling.`

Bad:
`I'll polish the layout.`

Bad:
`I'll make it nicer.`

## 7. Fix And Verify

Apply the smallest change that addresses the measured problem.

After editing, verify:
- the affected state
- one adjacent state
- relevant measurements when the issue was measurable
