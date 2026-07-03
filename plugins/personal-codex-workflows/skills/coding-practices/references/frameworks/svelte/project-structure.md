# Svelte/SvelteKit Project Structure

Use this reference when creating a Svelte/SvelteKit app, reorganizing frontend folders, or adding a feature large enough to need clear ownership boundaries.

## Default Shape

Prefer this structure for product apps:

```text
src/
  app.css
  app.html

  routes/
    +layout.svelte
    +layout.ts
    +page.svelte
    <route>/
      +page.ts
      +page.svelte

  lib/
    app/
      <app-shell-or-runtime-module>.svelte
      <app-session-or-navigation-module>.ts

    shared/
      ui/
      styles/
      types/

    features/
      <feature>/
        <feature>-types.ts
        <feature>-schemas.ts
        <feature>-client.ts
        <feature>-state.ts
        <FeaturePage.svelte>
        <FeatureSpecificPart.svelte>
```

Use this as a boundary guide, not a requirement to create empty folders.

## Ownership

- Keep `routes/` thin. Route files choose the screen, parse route params, load browser-safe data, and render feature components.
- Put app-wide shell, navigation, authenticated-route wiring, and runtime session coordination under `lib/app/` when it is shared across routes.
- Put product/domain UI and behavior under `lib/features/<feature>/`.
- Put only domain-neutral reusable UI under `lib/shared/ui/`.
- Put shared frontend primitives, tokens, and cross-feature types under `lib/shared/`.
- Do not create broad root-level `components/`, `utils/`, `helpers/`, or `common/` folders unless the folder has a precise contract.

## Feature Folder Pattern

Use a feature folder when code is owned by one product workflow or domain, such as auth, users, applications, dashboards, billing, or permissions.

```text
lib/features/auth/
  auth-types.ts
  auth-schemas.ts
  auth-client.ts
  auth-session.ts
  LoginForm.svelte
```

Keep the feature-local pieces together until at least two features need the same abstraction.

## Route Files

- `+page.ts` and `+layout.ts` own route params, query params, public env parsing, and route-load result shapes.
- `+page.svelte` and `+layout.svelte` should mostly compose components and handle route-level transitions.
- Do not hide product logic inside route files just because SvelteKit allows it.
- Use `+page.server.ts` only when the repo intentionally owns server-side frontend behavior and needs server-only secrets, cookies, or trusted backend calls.

## API And Validation Boundaries

- Keep API transport, decoding, and error normalization in `*-client.ts`.
- Keep schema validators in `*-schemas.ts` or `*-types.ts` when the feature is small.
- Validate API responses, route params, public env, storage, and generated-client outputs before components consume them.
- Use branded types for confusable primitives such as ids, tokens, URLs, timestamps, durations, and permission names.
- Do not parse external data inside `.svelte` components unless the component itself is the boundary.

## Shared UI Promotion

Start components inside the feature that needs them. Promote slowly:

1. Feature-specific component: `lib/features/<feature>/Thing.svelte`
2. Shared molecule: `lib/shared/ui/Thing.svelte`
3. Shared atom: `lib/shared/ui/Button.svelte`, `TextField.svelte`, `IconButton.svelte`

Only promote when another feature uses the component or when the component is clearly domain-neutral from the start.

## Naming

- Name feature files after the feature: `users-client.ts`, `users-types.ts`, `UsersPage.svelte`.
- Name app-wide modules by role: `AppShell.svelte`, `session.ts`, `navigation.ts`.
- Name shared UI by generic UI role, not product domain: `DataTable.svelte`, `Notice.svelte`, `Toolbar.svelte`.
- Avoid generic filenames such as `index.ts`, `types.ts`, or `client.ts` inside large folders when ownership would become ambiguous.

## When A File Is Too Large

Split a `.svelte` file when it owns more than one stable UI responsibility. Typical split points:

- shell layout versus page content
- list versus detail
- form container versus field group
- toolbar versus table
- dialog/drawer versus parent page
- data loading state versus record rendering

Prefer splits that preserve ownership. Do not create tiny abstractions that hide simple markup without reducing real complexity.
