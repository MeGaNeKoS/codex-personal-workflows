# Core Practices

Load this for every non-trivial change. It owns who decides structure, the change map, and the universal gates: rules that apply regardless of area, language, framework, or protocol.

This file states rules and one worked artifact. Branch references own the detail, the examples, and everything scoped narrower. Nothing here is restated by a branch.

## Who Decides

Structure is decided before the code, by whoever holds the whole picture. The coder builds inside it.

- **You were given a change map**, in a brief, a ticket, or the request. That map is the decision. Check every edit against it. When the work needs an owner, a dependency, or a boundary crossing the map does not name, stop and ask. Choosing one yourself is deciding architecture.
- **You are a subagent and a non-trivial task came with no map.** Ask the lead for one before editing.
- **You work directly for the user, or you are the lead writing a brief.** Draft the map yourself from the architecture document and the code, and state it before the first edit.

"Ask" in this skill means: ask the lead when you have one, and the user when you do not.

The declared owners and the allowed dependencies live in the project's architecture document: `ARCHITECTURE.md` at the project root, unless the project's instructions name another file. Read it before your first change in a repository. It also names the library the project uses for each concern and the command that lists a module's functions; its format is in [Architecture Document](./architecture-document.md). Where a project has none, the existing code is the declaration: follow its structure and do not invent a second one. A new project gets its architecture document before its first code. If it has none, ask for it.

These are decisions, and you ask before making any of them:

- a new module: a new package, crate, top level directory, or any unit the architecture document lists;
- a responsibility moving from one module to another;
- a new library or service the project depends on;
- a new table, field, or stored record the task did not name.

A new file inside an existing module, for a responsibility that module already has, is not a decision. Create it.

## General Engineering

- Match existing repository style and keep changes scoped to the requested behavior before introducing a new convention.
- Reuse before writing. A function or type that already does the job, in your module or in one it may call, is used and not written again. Looking is the first step of [Function Shape](./function-shape.md).
- Name domain concepts, protocol values, limits, defaults, and operational constants instead of scattering unexplained primitives. Keep each named value with the owner of the rule it expresses. Obvious indices and arithmetic identities stay inline. Detail in [Readability](./readability.md).
- Export only what code outside the module uses, in this change or already. Everything else stays private to its file or module.
- Prefer existing language, platform, framework, and repository primitives over a new library. For a concern the architecture document lists a library for, use that library. A new library is a decision under Who Decides: when the work seems to need one the task did not name, report what it is for and what it would replace, and do not add it.
- Do not create vague cross-owner buckets such as `utils`, `helpers`, or `common`. Use a named responsibility or a repository-designated neutral primitive family.
- Default to no comment. The allowed cases and the test are in [Readability](./readability.md).
- Code that runs in only one context (server only, browser only, inside a request, inside a transaction) learns that from one named place, such as a parameter or a single `isServer` module, never from checks scattered through the code.

## The Change Map

Five lines that fix the structure of a non-trivial change before any edit.

```text
Responsibility : which owner gains or loses behavior
Dependency     : each edge added, removed, or relied on, its direction,
                 and any edge that must not be added
Boundary       : where trust or representation changes, and who converts
Seam           : the public test seam for each changed responsibility
Reuse          : the existing functions and types the change calls,
                 each with its file
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
Reuse          : csv/encode encodeRows; billing/invoice Invoice, InvoiceLine,
                 listInvoices.
```

The forbidden edge is the point. If the CSV primitive needed to know about invoices, it is not a neutral primitive and the split is wrong.

The Reuse line is written from the list of what the module holds, never from memory. It is a floor: whoever builds still looks before adding a function, as [Function Shape](./function-shape.md) says.

The map uses four words in a fixed sense:

- **Owner**: the one module or file that holds a responsibility. Every behavior has exactly one.
- **Dependency**, or **edge**: one module importing or calling another. A third-party package the project installs is a library, and the map's Dependency line does not list it.
- **Boundary**: a place where data changes trust or representation, such as a request body becoming a domain type, or a database row becoming an entity.
- **Seam**: the public function or interface where a responsibility is tested or replaced.

## Universal Gates

Stop if a change requires any of these, and resolve it by asking, under Who Decides. Do not pick an answer and move on.

- **Undefined owner.** Every changed responsibility has exactly one named owner before editing. A new file inside an existing module, for a responsibility that module already has, needs no decision. A new module, or a responsibility moving to a module that did not have it, is an architecture decision.
- **Forbidden dependency.** An edge the change map or the architecture document forbids stays forbidden however it is reached. A barrel, alias, callback, context, store, cast, or service locator does not make an inverted edge valid.
- **Weak promotion.** Promotion is moving code out of the module that owns it into a shared one. Promote only when a second production caller, outside the module that owns the code, needs it today, and only to an owner the architecture names. A generic name, a repeated import, a large file, a test that calls it, or predicted reuse is not evidence.
- **Lost domain-safe value.** A value that was validated or parsed into a domain type stays in that type through internal code. Convert it to a raw string, number, or map only at a boundary that needs the raw form. Decode where trust or representation changes, not where a directory is named `api`.
- **Unjustified abstraction.** Add an interface, trait, or type parameter only when the code has two implementations today, when a test must replace a database, network, clock, or vendor call, or when one algorithm is reused over several types. One implementation and no fake means no abstraction.
- **Hidden runtime service.** A database, a network client, a clock, a random source, or any other runtime service reaches the code that uses it as an explicit parameter or constructor field, wired in one place. Do not inject a pure deterministic helper for symmetry.
- **Missing seam.** Each changed responsibility is tested through the seam its change map names, not through private helpers, so the tests show who owns the behavior. Add a compile-time test when the type system carries an invariant a runtime test cannot prove.
- **Silent failure.** Every error either propagates, or is handled with a stated reason and a structured log carrying context. `.ok()`, `unwrap_or_default()`, `let _ =`, an empty catch, or any other quiet fallback standing in for an error is not a substitute for handling it unless the design states why. A default in place of an error is never improvised.

If a change cannot proceed without violating a gate, report the structural conflict rather than routing around it.

## File Growth And Test Files

- Split along stable lines of responsibility. File size, file count, and directory count are audit signals, not reasons to split code that exists.
- A file over 1000 lines is full, whatever it holds, tests included. The gate applies to additions only. New behavior goes to the file that owns that responsibility, existing or new. A fix or small change to code already there stays put.
- A function reworked end to end moves to its owner, with its tests and call sites, unless moving it drags private state out or forces visibility open. Then it stays, and the report says why.
- Cohesion decides both ways: one function per file is as wrong as a huge file. A file holds one responsibility, usually several functions that change together.
- Splitting or reorganizing code you were not already changing is a sweep. It waits for the user to ask: name it in your report and leave it. Extracting the function you were already changing is not a sweep.
- A test file holds one behavior area with every case of it: positive cases in one file, negative cases in a sibling file, and never one file per case. Parsing a config file is one area; a missing key, a wrong type, and an empty file are cases of it and share the negative file. A second behavior area means a second file, however short the current one is. A test file that reaches 1000 lines splits by sub-behavior, positive and negative still apart. Where a language keeps unit tests inside the source file, its language branch says how this rule applies there.
