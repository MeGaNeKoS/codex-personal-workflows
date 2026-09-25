# Frontend Boundaries

Use when data crosses a trust, representation, process, or runtime boundary. This file owns the crossing itself. The client, port, or adapter that performs it belongs to [Integrations](./integrations.md).

## Boundary Pipeline

For each crossing:

1. name the boundary owner and external representation;
2. decode untrusted input and normalize representation-specific failures;
3. canonicalize values such as units, timestamps, URLs, and identifiers;
4. invoke validation or construction owned by the inward-facing contract; and
5. return the established feature or domain value without weakening it in later layers.

Apply this pipeline independently at every real boundary. "Decode once" means once per boundary crossing, not once for an entire application.

```text
API response      -> decode -> Invoice   (crossing 1: untrusted remote data)
localStorage read -> decode -> Invoice   (crossing 2: written by an older
                                          app version, or by another tab)
```

Skipping crossing 2 is the common bug. A value that was validated on the way in is not automatically valid on the way back out of storage.

## What Counts As A Boundary

Treat these as boundaries whenever trust or representation changes: remote responses, URL values, public runtime configuration, form/action payloads, persisted data, browser storage, worker messages, and cross-window messages.

- Keep wire DTOs, generated protocol types, SDK types, and framework payloads inside the boundary owner.
- Preserve domain-safe identifiers, units, canonical values, timestamps, and closed states through state, props, commands, callbacks, events, ports, and adapters.
- Keep immediate client interaction checks, authoritative API or server validation, and transport or operation failures distinct. Return explicit outcomes the state or workflow owner can handle without parsing adapter-specific exceptions.

**Also load for TypeScript:** [Runtime And Domain Values](../../languages/typescript/runtime-and-domain-values.md) when the boundary validates external data or constructs domain-safe values.
