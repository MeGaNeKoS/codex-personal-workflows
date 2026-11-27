# Frontend UI Patterns

Use when a change adds or alters interaction states, forms and mutations, or tables and other data views.

## Interaction States

- Show loading, refreshing, empty, no-results, failed, retryable, permission denied, unavailable, not configured, disabled with reason, validation failed, stale data, partial data, and pending mutation states close to affected content.
- Do not rely on toast-only feedback for important outcomes.
- Keep disabled states understandable when the reason affects user action.
- Preserve layout stability when entering loading or error states.

## Forms And Mutations

- Make validation specific and field-level where possible.
- Preserve user input after failed submission.
- Separate validation errors from permission, conflict, and server failures.
- Confirm destructive, irreversible, security-sensitive, or audit-sensitive actions.
- Explain consequences before confirmation.
- Keep submit buttons stable during loading; do not shift layout when showing errors.

## Tables And Data Views

- Use real table semantics for tabular data.
- Keep headers visible or easily recoverable for large tables.
- Make sorting, filtering, pagination, and selected state explicit.
- Support empty, loading, failed, and permission-denied table states.
- Avoid cramming all fields into mobile tables; provide row summaries and detail views.
- Keep row actions keyboard accessible and discoverable.
- Use server-side pagination/filtering for large datasets unless client-side behavior is explicitly required.

## Accessibility Checks

Accessibility rules are owned by [Accessibility](./accessibility-diagnostics.md). **Also load:** that file when a change adds or modifies a control, a form, a dialog, or color that carries meaning.

## Verification

- Run the app locally when possible.
- Inspect with browser snapshots or screenshots.
- Check at least one desktop and one narrow/mobile viewport.
- Verify no console errors.
- Verify keyboard focus for new interactive controls.
- Verify text does not overflow or overlap.
- Verify loading, empty, and error states if they were touched.
