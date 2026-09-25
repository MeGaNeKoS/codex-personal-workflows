# Evidence Contract

This file owns the full evidence contract for frontend-delivery artifacts: research records, implementation plans, and verification reports. The SKILL.md label definitions summarize this contract; nothing here is restated elsewhere.

## Labels

Label every conclusion exactly as **Observed**, **Inferred**, **Proposed**, or **Unknown**:

- **Observed:** directly established by cited source, configuration, test, runtime, or user-provided artifact/input.
- **Inferred:** reasoned from cited observations, with the reasoning stated; not presented as direct fact.
- **Proposed:** a target-design recommendation or choice, not reference behavior.
- **Unknown:** not established; record what was inspected, the concrete blocker, and the evidence needed.

Every factual claim needs traceable evidence. Prefer evidence in this order: source/tests, runtime, design files, recordings/screenshots, then descriptions. A visual resemblance does not prove component composition, ownership, semantics, state, or persistence.

For each runtime claim, record revision/build, environment and viewport, inputs, feature flags, permissions, fixtures/data, and exact verification procedure. For each source claim, record repository revision and file/symbol or test. Subjective transfer recommendations are **Proposed**.

## Worked Example

A labeled finding looks like this:

```text
Observed  The invoice table renders 50 rows per page.
          src/billing/InvoiceTable.tsx:31 (PAGE_SIZE = 50)

Observed  No empty state exists. The tbody renders zero <tr> when rows is [].
          src/billing/InvoiceTable.tsx:58-64

Inferred  Sorting is server-side. The component sends `sort` to the query
          hook and never sorts locally (InvoiceTable.tsx:44), and the handler
          accepts a sort param (api/invoices.ts:12). Not confirmed at runtime.

Unknown   Behavior past 10k invoices. Inspected the table, query hook, and
          handler; no limit or virtualization found. Blocker: no fixture or
          environment with that volume. Needed: a seeded dataset or a
          production row-count figure.

Proposed  Add an empty state with a "Create invoice" action.
          Design recommendation, not reference behavior.
```

The labels carry weight only if **Unknown** is used honestly. An **Unknown** that states what was inspected and what would resolve it is worth more than an **Inferred** that is really a guess.

## Process Guarantee

For the same relevant accessible source/config/tests/runtime at the same revision, environment, inputs, flags, fixtures, and verification procedure, agents must reach the same verified factual behavior conclusions. Subjective design recommendations may differ only as **Proposed**. Conflicting factual conclusions fail until resolved through source, configuration, tests, or runtime, or recorded as concretely blocked. An **Unknown** must state what was inspected, the concrete blocker, and the evidence needed to resolve it.
