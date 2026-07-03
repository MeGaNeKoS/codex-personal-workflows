# Svelte/SvelteKit Framework

Use this branch for Svelte and SvelteKit components, routing, layouts, load functions, forms, progressive enhancement, SSR/CSR boundaries, stores, runes, and UI packages.

## Architecture

- Use SvelteKit for routing, layouts, route data, error boundaries, and deployment structure when the project is an app.
- Keep backend product policy out of SvelteKit server code unless the repo explicitly owns that backend responsibility.
- Keep reusable UI in component packages or local component folders with clear ownership.
- Keep API boundary code separate from components.
- Avoid global state until state must be shared across routes or distant components.
- Prefer explicit small modules over broad utility files.
- When creating a Svelte/SvelteKit app, reorganizing folders, or adding a feature large enough to need ownership boundaries, read `project-structure.md`.

## Components

- Keep components focused on one UI responsibility.
- Use props for parent-provided state and callbacks/events for parent-owned changes.
- Keep derived display values close to the component unless they are domain rules.
- Prefer slots/snippets for layout composition instead of over-general prop APIs.
- Avoid component abstractions that hide important interaction or accessibility behavior.
- Use semantic HTML before custom ARIA.

## State

- Use local component state for local UI behavior.
- Use route `load` data for route-owned server data.
- Use stores or Svelte 5 runes for shared client state only when ownership is clear.
- Keep polling, refresh, and async job state in named helpers or route-level modules.
- Avoid adding a client query/state library until repeated server-state behavior proves it is needed.

## Routing And Data

- Use `+layout.svelte` for persistent app shell and shared layout.
- Use `+page.svelte` for route UI.
- Use `+page.ts` or `+layout.ts` for browser-safe data loading.
- Use server load functions only when server-only secrets, cookies, or trusted server behavior are actually required.
- Validate route params and external data at boundaries.
- Represent not-found, permission, unavailable, and failed states explicitly.
- Use `invalidate` and targeted reload behavior instead of full-page refreshes.

## Forms

- Prefer native form behavior and progressive enhancement where it fits.
- Preserve user input on validation failure.
- Keep field-level errors close to fields.
- Separate client validation from API validation and server failures.
- Confirm destructive, irreversible, security-sensitive, or audit-sensitive actions.

## TypeScript And Validation

- Keep TypeScript at maximum practical strictness.
- Do not weaken compiler options, add broad casts, or introduce `any` to escape difficult typing problems.
- When a typing problem is complex, first understand the real data/model boundary and explain the issue.
- If the correct type model is unclear or requires a product/API decision, consult the user instead of hiding the problem.
- Use typed props, typed events/callbacks, and typed route data.
- Validate external data at the API/client boundary.
- Keep branded/domain types in shared API or domain packages, not scattered across components.
- Avoid `any`; if unavoidable, keep it at the boundary and convert to trusted types immediately.

## Styling And Accessibility

- Prefer component-scoped CSS and shared design tokens.
- Use global CSS for reset, tokens, app shell primitives, and typography only.
- Avoid depending on a full visual framework unless the project already chose one.
- Use responsive CSS deliberately; verify desktop and mobile layouts.
- Keep focus styles visible and consistent.
- Use native controls where possible.
- Add accessible names for icon buttons.
- Manage focus for modals, drawers, menus, and route transitions.
- Ensure keyboard navigation works for custom interactive components.
- Avoid color-only status.
- Fix Svelte accessibility warnings instead of suppressing them.

## Dependency Policy

- Prefer Svelte, browser APIs, and small project-local helpers.
- Add dependencies only when they remove meaningful complexity or cover accessibility-sensitive behavior.
- Prefer CodeMirror for custom query/code editors when a textarea is no longer enough.
- Defer charting, table, form, and state libraries until requirements justify them.

## Verification

- Run typecheck and build after structural changes.
- Run the local app and inspect affected routes.
- Check browser console errors.
- Verify mobile and desktop layouts.
- Verify keyboard focus and basic screen-reader semantics for new interactions.
