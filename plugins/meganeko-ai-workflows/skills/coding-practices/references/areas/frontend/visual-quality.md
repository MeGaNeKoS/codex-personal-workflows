# Frontend Visual Quality

Use this reference when a change defines or alters layout structure, sizing, density, responsive behavior, or visual hierarchy. It owns design-time layout rules. Reported visual defects are diagnosed through [Layout Diagnostics](./layout-diagnostics.md); styling-system choice belongs to [Styling](./styling.md).

## Sizing And Responsive Structure

- Define stable dimensions for repeated UI elements such as tables, toolbars, boards, tiles, panels, and counters.
- Use responsive constraints such as grid tracks, `minmax`, spacing clamps, aspect ratios, and container-aware layout rules.
- Do not scale font size directly with viewport width.
- Ensure text fits its container at mobile and desktop widths.
- Prevent overlap between navigation, headers, toolbars, content, and overlays.
- Prefer scrolling regions only when the scroll boundary is obvious and keyboard reachable.
- For narrow screens, convert dense horizontal layouts into stacked sections, priority columns, drawers, or summary/detail flows.

```text
Wrong: font-size: 4vw
       text is unreadable at 320px and absurd at 2560px

Right: font-size: clamp(1rem, 0.9rem + 0.4vw, 1.25rem)
       bounded at both ends, scales in the middle
```

## Density And Hierarchy

- Match visual density to the task and audience. Operational interfaces stay dense, scannable, and predictable.
- Use restrained color and reserve strong color for status, risk, and action.
- Keep typography hierarchy clear without oversized headings inside compact UI.
- Avoid nested cards, decorative background effects, and marketing-style composition inside task surfaces.
- Use icons for recognizable actions, and labels when ambiguity would slow users down.

## Relationship To Design Work

This reference owns the structural rules a change must satisfy. Product framing, interaction design, visual identity, and runtime visual verification belong to the active frontend-delivery workflow. When the two disagree on a factual claim about current behavior, resolve it through source, configuration, tests, or runtime rather than preference.
