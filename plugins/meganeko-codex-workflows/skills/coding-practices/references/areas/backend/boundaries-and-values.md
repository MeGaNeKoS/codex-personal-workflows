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

## Authority For Mechanics

- Treat the active language reference as the exclusive authority for parsing, validation, construction, casting, unchecked conversion, and other language-level escape-hatch mechanics.
- Follow that language reference when an unchecked conversion is permitted; keep it in the smallest adapter where its boundary invariant is observable, document the invariant there, and never expose the unchecked representation inward.
- Do not duplicate language-specific validation or cast policy here.
