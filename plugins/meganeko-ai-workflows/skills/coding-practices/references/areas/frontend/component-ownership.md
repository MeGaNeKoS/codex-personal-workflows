# Frontend Component Ownership

Use when deciding whether UI belongs to a feature or a neutral design-system owner. Moving anything to a broader owner is a separate decision owned by [Promotion](./promotion.md).

## Placement

- Keep product-specific tables, drawers, dialogs, forms, status controls, and presentational sections with the feature that owns their behavior.
- Keep derived display values close to the component presenting them. Move a derivation only when it is a domain rule or another named owner must share it.
- A visual role or generic filename does not establish design-system ownership.

## What Makes A Primitive Neutral

A neutral primitive contains **no** feature copy, permission, workflow, domain status, command, remote data shape, or product-specific styling policy.

```text
Neutral        <Dialog>          open, onClose, labelledBy, children
Feature-owned  <CancelPlanDialog>  knows the plan, the warning copy,
                                   the permission check, the mutation
```

`CancelPlanDialog` wraps `Dialog`. The wrapper stays feature-owned; the primitive stays ignorant of billing. Adding a `variant="cancel-plan"` prop to `Dialog` is the failure mode: it moves product policy into the neutral owner.

- The design-system contract owns the primitive's interaction, focus, keyboard, labeling, semantics, and compositional accessibility behavior.
- Atomic or primitive naming may organize a design system. It does not establish a global hierarchy for product code.
- Do not introduce an abstraction that hides required interaction or accessibility behavior behind a generic API.
