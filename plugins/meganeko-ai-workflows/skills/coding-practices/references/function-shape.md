# Function Shape

Load before any code change, to catch one function doing the work of two. Cross-cutting: applies to any area or language.

## Tiers

Tier a function by what it needs in order to run.

- **Compute.** Needs only its arguments. Branches, calculates, decides. No call to a database, the network, the filesystem, the clock, a random source, or another process. Testable with zero fakes.
- **Adapt.** Needs only its arguments, and its job is shape translation across a boundary: request body to domain type, row to entity, domain error to transport status. Makes no decisions.
- **Orchestrate.** Needs the other functions. Calls them in order and passes results along. It may return early on an error, and it may branch on an answer a called function already decided. It holds no product rule of its own: no limit, no eligibility check, no calculation, no comparison of domain values. Testable with fakes at each seam.
- **Effect.** Needs the outside world. The actual database call, network call, or file read or write, plus turning its failure into the caller's error type, and nothing else.

```go
// Compute: the rule, as values in and a value out.
func exceedsAllowance(addedLines, allowance int) bool {
	return addedLines > allowance
}

// Effect: the outside world, and nothing else.
func readTarget(path string) (string, error) {
	raw, err := os.ReadFile(path)
	return string(raw), err
}

// Orchestrate: the sequence. It acts on answers and decides nothing itself.
func denyOversizedEdit(edit Edit, limits Limits, out io.Writer) error {
	existing, err := readTarget(edit.Path)
	if err != nil {
		return err
	}
	if !exceedsAllowance(countAddedLines(edit, existing), limits.Allowance) {
		return nil
	}
	return writeDenial(out, edit.Path, limits)
}
```

`denyOversizedEdit` branches, but only on an error and on the boolean `exceedsAllowance` returned. Writing `countAddedLines(edit, existing) > limits.Allowance` inside it would put the rule in the orchestrator.

## The Mixed Function Test

A function that both decides a product rule and touches the outside world is two functions, in any language, whether the call is awaited or not. Pull the decision out as compute, leave the call as effect, and let an orchestrator hold the sequence.

Any one of these signals a mixed function:

- a comparison or calculation on domain values beside a database, network, file, or clock call;
- a test that needs a fake only to reach a branch;
- a name that needs "and".

Where the pieces go: the compute function moves to the file that owns the rule, the effect to the file that owns the technology (the repository, the client, the file reader), and the orchestrator stays where the use case lives.

Other mixes, such as an adapter that also calculates, are a signal to check that the function still has one job, not a breach on their own.

## Size

- A function works at one level. Either it states the steps, one call per step, which is an orchestrator, or it does one step.
- A body that mixes the two, with some steps written out inline between the calls, gets each inline step extracted under the name you would use to describe it. What remains is an orchestrator.
- A function that no longer fits on one screen is a prompt to check the two rules above, not a line limit.

File length is gated separately, in [Core Practices](./core.md).

## The Payoff

Compute and adapt need no fakes, so integration and behavioral tests only have to cover orchestrate and effect.
