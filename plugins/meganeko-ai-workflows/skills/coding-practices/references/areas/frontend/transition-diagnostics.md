# Frontend Transition Diagnostics

Use this before changing animation or transition code when the user reports movement, collapse/expand, drawer, sidebar, panel, hover motion, jank, or timing weirdness.

## 1. Diagnose Motion Across Time

Do not rely on one screenshot for transition bugs.

Inspect:
- the start state
- one or more during-transition states
- the end state

If the reported motion issue is unclear, ask what looks wrong: timing, direction, alignment, jitter, snapping, delay, easing, or element order.

## 2. Identify The Transition Owner

Determine which element owns the transition and which elements visually move because of it.

Inspect:
- transition properties and durations
- start and end computed values
- parent and sibling layout changes
- hidden children that still affect intermediate layout
- whether the animated property is layout-affecting, paint-affecting, transform, or opacity

Prefer explicit start/end values. Be careful with `auto` sizes, grid/flex tracks, content-driven dimensions, and hidden labels.

## 3. Sample The Transition

When browser tooling is available, trigger the transition and sample geometry across time.

Measure the same elements before, during, and after the transition:
- moving region
- sibling region
- hover/focus target
- icon or text inside the target
- hidden/collapsed children
- scroll or clipping container

For alignment issues during motion, compute child center against the actual target center at multiple frames.

For user-visible motion complaints, prefer high-frequency geometry sampling over a low-frame-rate recording alone. A 24fps video or filmstrip can miss one-frame snaps on 60Hz, 120Hz, or 144Hz displays. Use `requestAnimationFrame` sampling when possible, then save a small clipped filmstrip or slowed screenshots only as human-readable evidence.

Sample visible descendants, not only parent boxes. Include text labels, icons, and hidden/collapsed children because a parent can animate smoothly while a child reflows, jumps rows, changes line boxes, or snaps its width/height.

## 4. Slow Motion When Needed

If the motion is hard to inspect, temporarily slow transitions in the browser or dev-only CSS. Slow every element that participates in the motion, including the real transition owner, parents that change layout variables, and descendants that animate inside it.

Example debug CSS:

```css
.debug-slow-motion *,
.debug-slow-motion *::before,
.debug-slow-motion *::after {
  transition-duration: 1500ms !important;
  animation-duration: 1500ms !important;
}
```

Remove debug-only slowdown before finishing unless the user explicitly asks to keep it.

Do not trust a slow-motion result if only part of the transition was slowed. For example, slowing a sidebar's children while the shell grid column still changes at normal speed can create false clipping, drifting, or "entering from outside" artifacts that do not exist in the real animation.

If browser sampling is throttled, unreliable, or cannot write video frames, combine:
- high-frequency box measurements returned from the page
- clipped screenshots of the affected region
- temporary slow motion to inspect the visual path

## 5. Check Motion-Specific Failure Modes

Look for:
- snapping at start or end
- sibling layout jumping instead of resizing smoothly
- icons or text drifting off-center
- labels disappearing too early or too late
- hover/focus targets changing size unexpectedly
- clipped content flashing during collapse/expand
- multiple properties fighting each other
- transitions running on first render or refresh unintentionally
- missing or incorrect `prefers-reduced-motion` handling
- text reflowing into a different grid/flex row before opacity or width finishes
- non-animatable state changes such as `display`, grid placement, `justify-items`, or `auto` to numeric size changes
- correct start/end states with an incoherent intermediate path

## 6. State The Diagnosis

Before editing, state the concrete motion diagnosis in plain terms.

Good:
`The rail width animates, but the footer label remains in the grid until the end, so the icon travels upward during collapse.`

Good:
`The main pane jumps because its grid column changes after the sidebar transition instead of sharing the same transition.`

Bad:
`I'll smooth the animation.`

## 7. Fix And Verify

Apply the smallest change that addresses the measured motion problem.

After editing, verify:
- start state
- during-transition state, by sampling or slowed motion
- end state
- adjacent state, such as expanded after collapsed or hover after default
- `prefers-reduced-motion` behavior when relevant

For issues the user noticed visually, report what was sampled and at what effective rate. If the verification used only a low-frame-rate capture, state that limitation instead of treating it as conclusive.
