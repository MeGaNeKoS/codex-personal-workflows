# Backend Boundaries And Values

Use this reference when backend data crosses a protocol, persistence, configuration, external-service, or runtime boundary. It owns placement and retention of domain-safe values; the active language reference owns language mechanics.

## Boundary Ownership

- Validate protocol, persistence, configuration, and external-service values at the boundary that owns their representation.
- Invoke the parser or constructor exposed by the inward-facing domain contract. Map the external representation before passing the established value inward.
- Carry domain-safe identifiers, units, canonical values, timestamps, and closed states through use cases, services, ports, repositories, and adapters.
- Convert to protocol or persistence representations only inside the adapter that owns that representation.
- Keep framework types, wire DTOs, generated protocol types, SDK types, and persistence records out of domain and application contracts.
- Apply boundary decoding independently at every trust or representation crossing; a later persistence, message, worker, or external-service crossing may require another conversion.

## Domain-Safe Retention

- Preserve established domain values through internal layers until another boundary requires conversion.
- Do not weaken branded, canonical, unit-bearing, timestamp, or closed-state values to unqualified strings or numbers inside application code.
- Keep external representations and their normalization failures inside the smallest adapter that can observe them.

### Applied

The failure mode is a domain value that gets flattened back to a primitive somewhere in the middle, so every later layer has to re-establish the invariant or silently trust it.

```text
Wrong: the value decays, and the last layer cannot tell validated from raw.

  HTTP adapter    parse -> Email          (validated once)
  service         (email: string)         <-- decayed here
  repository      (email: string)
  notifier        sendTo(email: string)   <-- is this validated? unknowable

Right: the value is established once and carried.

  HTTP adapter    parse -> Email
  service         (email: Email)
  repository      (email: Email)          -> toColumn(email) at the SQL edge
  notifier        sendTo(email: Email)
```

Each crossing decodes independently. A value that arrived validated over HTTP is *not* automatically valid when it comes back out of the database later:

```text
HTTP request   -> decode -> Email   (crossing 1: untrusted input)
Postgres row   -> decode -> Email   (crossing 2: storage may predate the rule,
                                     or have been written by another writer)
```

Skipping crossing 2 is the common bug. See the active language reference for when a trusted constructor may replace parsing there.

## Authority For Mechanics

- Treat the active language reference as the exclusive authority for parsing, validation, construction, casting, unchecked conversion, and other language-level escape-hatch mechanics.
- Follow that language reference when an unchecked conversion is permitted; keep it in the smallest adapter where its boundary invariant is observable, document the invariant there, and never expose the unchecked representation inward.
- Do not duplicate language-specific validation or cast policy here.
