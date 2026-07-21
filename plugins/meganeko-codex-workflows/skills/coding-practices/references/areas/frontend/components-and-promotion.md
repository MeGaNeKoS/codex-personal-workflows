# Frontend Components And Promotion

Use this reference when deciding whether UI belongs to a feature or a neutral design-system owner, or when moving any frontend responsibility to a broader owner. Placement below is UI-specific; promotion applies to components, contracts, state, clients, and runtime-neutral code.

## Placement

- Keep product-specific tables, drawers, dialogs, forms, status controls, and presentational sections with the feature that owns their behavior.
- Keep derived display values close to the component presenting them. Move a derivation only when it is a domain rule or another named owner must share it.
- A visual role or generic filename does not establish design-system ownership.
- A neutral primitive contains no feature copy, permission, workflow, domain status, command, remote data shape, or product-specific styling policy.
- The design-system contract owns the primitive's interaction, focus, keyboard, labeling, semantics, and compositional accessibility behavior.
- Wrap a neutral primitive in a concrete feature component when product policy is added. The wrapper remains feature-owned.
- Atomic or primitive naming may organize a design system. It does not establish a global hierarchy for product code.

Do not introduce an abstraction that hides required interaction or accessibility behavior behind a generic API.

## Promotion

Promote code only when all conditions hold:

1. the destination is a repository-designated owner or an explicitly approved neutral primitive family;
2. the destination contract matches the behavior and dependencies;
3. a separate production consumer exists, or neutral ownership is a recorded product architecture decision;
4. feature policy stays outside the promoted API;
5. dependency direction remains valid; and
6. the public API does not expose former-owner internals.

Do not promote code because two files look similar, a file is large, its name is generic, imports repeat, or another consumer may appear. Update ownership/import checks and tests when promotion changes a public boundary.
