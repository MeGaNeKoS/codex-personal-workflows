# Backend Area

Use this branch for backend architecture, transport handlers, application services and use cases, domain modules, repositories, outbound clients, dependency construction, error models, and backend test seams.

Apply the guidance by responsibility. Translate it into the current project and language instead of copying a folder structure or layer template blindly.

## Architecture Routing

Select every row matched by the change. An explicit **Also load** instruction in a focused reference adds another reference; ordinary links do not.

| Changed concern | Load |
| --- | --- |
| Transport, application/use-case, domain, repository, outbound, or infrastructure ownership; dependency direction; port placement; repository-versus-outbound classification | [Architecture And Dependencies](./architecture-and-dependencies.md) |
| Protocol, persistence, configuration, or external-service values; representation conversion; adapter placement; retention of domain-safe values | [Boundaries And Values](./boundaries-and-values.md) |
| Composition root, mandatory dependency construction, long-lived clients, dependency aggregation, startup, shutdown, or runtime lifecycle | [Composition And Lifecycle](./composition-and-lifecycle.md) |
| Backend files, modules, public roots, constants, responsibility-based splitting, or names | [Modules And Naming](./modules-and-naming.md) |
| Product error shape, stable error codes, response formatting, or transport error mapping | [Errors And Responses](./errors-and-responses.md) |
| Unit or integration responsibility, fakes, wiring tests, adapter tests, or migration compatibility | [Testing](./testing.md) |

Also load the focused language references matching the implementation concerns. The backend branch owns responsibility placement and dependency direction; the active language reference owns language-specific parsing, validation, construction, type, cast, and escape-hatch mechanics. Load the active framework/library reference for concrete runtime mapping and the active protocol reference for wire-specific contracts and behavior.

## Backend Architecture Gates

- Every changed responsibility must have one named owner and an allowed dependency direction before editing.
- Transport adapts protocol input and output; application services and use cases orchestrate; domain code owns product invariants; infrastructure adapters own concrete technology.
- Repositories represent product-owned storage. Remote services, vendors, identity systems, notification providers, and other externally owned systems are outbound clients.
- External representations are converted at the adapter that owns them. Domain-safe values remain intact through application, domain, and port contracts.
- Mandatory dependencies are explicit, and long-lived concrete clients are constructed at a composition root rather than inside handlers or services.
- Product errors are stable and transport-independent until a boundary maps them into the active protocol.
- Tests must exercise the same ownership boundaries and replacement seams used by production code.

These are routing gates. Focused references own the detailed backend architecture policy and must not be copied back into this index.

Combine this branch with the relevant language, framework/library, and protocol branches.
