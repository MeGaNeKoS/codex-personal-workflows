# Architecture Document

Load when writing or changing a project's architecture document. It owns the document's format. Who decides a change to it is in [Core Practices](./core.md).

The document is `ARCHITECTURE.md` at the project root, unless the project's instructions name another file. It is a map of the repository as it is now: no history, no argument for a decision, no plan. It stays short enough to read before the first change in a session, so a module is one table row, never a section of prose.

## Format

One opening paragraph, then five sections with these headings, in this order.

````markdown
# Architecture

Invoicing service: an HTTP API over one database. Execution starts in `server` (the API) and `worker` (scheduled jobs).

## Modules

| Module | Path | Owns | Tested through |
| --- | --- | --- | --- |
| server | `cmd/server/` | wiring and startup of the API | a start and stop smoke test |
| worker | `cmd/worker/` | wiring and startup of scheduled jobs | a start and stop smoke test |
| api | `internal/api/` | HTTP routing, request decoding, response encoding | handler tests with a fake billing service |
| billing | `internal/billing/` | invoices, payments, and the rules for both | `billing.Service` with a fake store |
| store | `internal/store/` | SQL queries and row mapping | queries against a test database |
| csv | `internal/csv/` | CSV encoding, with no domain knowledge | `csv.Encode` |

## Dependencies

server -> api, billing, store
worker -> billing, store
api -> billing
billing -> store, csv

## Boundaries

| Where | From | To | Converted by |
| --- | --- | --- | --- |
| HTTP request | JSON body | `billing.NewInvoice` | api |
| Database row | `store.invoiceRow` | `billing.Invoice` | store |

## Libraries

| Concern | Library | Used by |
| --- | --- | --- |
| HTTP routing | `chi` | api |
| SQL | `pgx` | store |
| Money | `shopspring/decimal` | billing |

## Function Map

```bash
ctags -R -x --_xformat='%F:%n  %K  %s %N%S %t' --languages=Go --kinds-Go=fsitacv --exclude='*_test.go' <module path>
```
````

## What Each Section Holds

- **Opening paragraph.** What the system is and where execution starts, in one or two sentences.
- **Modules.** One row per module. A module is a unit other code depends on as a whole: a package, a crate, or a top level directory. "Owns" states one responsibility. "Tested through" names the seam.
- **Dependencies.** One line for each module that calls another: the module, an arrow, and the modules it may call. Every name on a line is a row of the Modules table. An edge that is not listed is forbidden. The graph has no cycle.
- **Boundaries.** Each place where data changes trust or representation, and the one module that converts.
- **Libraries.** One library per concern, and the modules that use it. A concern with no row has no library yet, and adding one is a decision. Where the code uses two libraries for one concern today, both get a row, and the Concern cell of each says which one new code uses.
- **Function Map.** The command that lists one module's functions and types for this repository, taken from [What Exists](./what-exists.md) with this repository's excludes filled in. Its output is produced when someone needs it and is never pasted into the document.

## Keeping It True

A change that adds a module, an edge, a boundary, or a library changes the document in the same change, once that decision has been made by whoever decides it. A document that disagrees with the code is a defect: report it.
