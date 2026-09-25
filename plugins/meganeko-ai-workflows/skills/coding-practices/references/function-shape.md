# Function Shape

Use when writing or reviewing a function, to catch one function doing the work of two. Cross-cutting: applies to any area or language.

## Tiers

Tier a function by what it needs in order to run.

- **Compute.** Needs only its arguments. Branches, calculates, decides. No external await, no clock, no randomness. Testable with zero fakes.
- **Adapt.** Needs only its arguments, and its job is shape translation across a boundary: request body to domain type, row to entity, domain error to transport status. Makes no decisions.
- **Orchestrate.** Needs the other functions. Calls them in order and passes results along. No branching on domain rules and no calculation of its own. Testable with fakes at each seam.
- **Effect.** Needs the outside world. The actual database call, network call, or file write, plus its error mapping, and nothing else.

## The God Function Test

A function that both branches on domain rules and awaits an external call is two functions. Pull the decision out as compute, leave the call as effect, and let an orchestrator hold the sequence.

A god function is one holding more than one tier. Separately, a file is gated on length, owned by [Core Practices](./core.md).

## The Payoff

Compute and adapt need no fakes, so integration and behavioral tests only have to cover orchestrate and effect.
