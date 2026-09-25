# Core Practices

Load this for every non-trivial change. It owns the universal gates: rules that apply regardless of area, language, framework, or protocol.

This file states rules and one worked artifact. Branch references own the detail, the examples, and everything scoped narrower. Nothing here is restated by a branch.

## General Engineering

- Match existing repository style and keep changes scoped to the requested behavior before introducing a new convention.
- Name domain concepts, protocol values, limits, defaults, and operational constants instead of scattering unexplained primitives. Keep each named value with the owner of the rule it expresses. Obvious indices and arithmetic identities stay inline.
- Keep public APIs intentional and small.
- Prefer existing language, platform, framework, and repository primitives. Add a dependency only when it removes meaningful complexity or supplies mature behavior that is risky to implement locally.
- Do not create vague cross-owner buckets such as `utils`, `helpers`, or `common`. Use a named responsibility or a repository-designated neutral primitive family.
- Default to no comment. Prefer a better name, a named constant, or an extracted function over explaining code in prose. Comment only when the reason is genuinely unrecoverable from the code, such as an external system's quirk, an approach that was tried and broke, or an invariant enforced elsewhere. Update or delete a comment in the same edit that changes what it describes. Detail in [Readability](./readability.md).
- Prefer explicit control flow when a decision has multiple branches, validation, fallback, or side effects. Extract a named resolver rather than nesting conditional expressions.
- Make assumptions about execution context explicit, behind a clearly named boundary.

## The Change Map

Before editing, state four things. This is a thinking tool, not a deliverable.

```text
Responsibility : which owner gains or loses behavior
Dependency     : each edge added, removed, or relied on, and its direction
Boundary       : where trust or representation changes, and who converts
Seam           : the public test seam for each changed responsibility
```

Worked example, adding "export invoices as CSV" to a billing feature:

```text
Responsibility : billing feature owns the export command and its UI trigger.
                 CSV encoding is a neutral primitive (no billing knowledge).
Dependency     : billing/export -> csv primitive        (allowed, neutral)
                 billing/export -> billing/invoice types (allowed, same owner)
                 csv primitive  -> billing               (FORBIDDEN, would invert)
Boundary       : Invoice (domain-safe) -> CSV rows (external representation),
                 converted in billing/export, not inside the csv primitive.
Seam           : billing/export public function, given fixture invoices.
```

The forbidden edge is the point. If the CSV primitive needed to know about invoices, it is not a neutral primitive and the split is wrong.

## Universal Gates

Stop and resolve explicitly if a change requires any of these.

- **Undefined owner.** Every changed responsibility has exactly one named owner before editing. A newly proposed owner is an architecture decision; giving a responsibility the code already has its own file, because none fits, is not one. Moving a responsibility to a module that did not have it is.
- **Forbidden dependency.** A barrel, alias, callback, context, store, cast, or service locator does not make an inverted edge valid.
- **Weak promotion.** Promote only to a designated owner with a real production consumer outside the current owner. A generic name, repeated import, large file, or predicted reuse is not evidence.
- **Lost domain-safe value.** A validated value stays validated through internal layers until a boundary requires conversion. Decode where trust or representation changes, not where a directory is named `api`.
- **Unjustified abstraction.** Introduce an abstraction or type parameter only when it preserves a real relationship, isolates an external dependency, supports substitutable implementations, or implements a reused algorithm.
- **Hidden dependency.** Replaceable external clients, clocks, and runtime services pass through an explicit contract at the owning composition seam. Do not inject a pure deterministic helper for symmetry.
- **Missing seam.** Test responsibility reveals the production owner and boundary seam. Add compile-time tests when the type system carries an invariant runtime tests cannot prove.
- **Silent failure.** Every error either propagates, or is handled with a stated reason and a structured log carrying context. `.ok()`, `unwrap_or_default()`, `let _ =`, an empty catch, or any other quiet fallback standing in for an error is not a substitute for handling it unless the design states why. A default in place of an error is never improvised.

Split at stable responsibility seams. File size, file count, and directory count are audit signals, not thresholds for splitting existing code. A file over 1000 lines is full regardless of what it holds; that gate applies to additions only, never to splitting or reorganizing what already exists, which waits for the user to ask for it. A small extraction, the function you were already changing, needs no such wait; splitting or reorganizing anything you were not already editing is a sweep. New behavior goes to the seam owner that covers it, existing or new; a fix or small change already there stays put. A function reworked end to end moves to its owner, with its tests and call sites, unless that drags out private state or forces visibility open, then it stays, reason reported. Cohesion decides both ways: one function per file is as wrong as a huge file. A test file is bounded by scope, not length, and full at 1000 lines like any other file: one behavior per file, positive and negative apart, a second area means a second file regardless.

If a change cannot proceed without violating a gate, report the structural conflict rather than routing around it.
