# Function Shape

The detail and examples behind the [Coder Card](./coder-card.md)'s lines on planning a function and one job per function. It owns how a function comes to exist: what to look at before writing one, the order to write in, and the test that each function does one job. Cross-cutting: applies to any area or language.

## Write In This Order

Plan the functions before they exist. A change that adds a function, or changes which function calls which, goes through these five steps in order. A change that stays inside one existing body goes straight to step 5.

1. **Look at what exists.** **Also load** [What Exists](./what-exists.md), and list what your module and the modules it may call already hold. Do not read a long list: filter it by the noun, the thing your function works on. Search by the noun and not the verb, since a planned `loadInvoice` is only found as `fetchInvoice` through `invoice`. Use what is already there, types and constants included: an existing `OrderId` beats a new `string`. The functions your brief names for reuse come first. The project's libraries count too: before writing something a library the project uses might already do, such as routing, a timeout, a retry, or parsing, look it up there. When the library does most of it, wrap the library and add the missing part. Your own version of the whole needs a reason no wrapper can meet, stated in your report. A reviewer checks that reason against the library, and several defects in one own version mean replace it with the wrapper, not patch it.
2. **Write the top function first, out of calls.** Write the function the caller needs as a sequence of calls, each named for what that step should do, whether or not the function exists yet. One call per step. Read it back: it is the plan for the change, and if it does not tell the behavior in order, fix the names before going on.
3. **Give every missing function a placeholder.** Each call to a function that does not exist yet gets one: the real signature, with types, and a body that fails loudly. Use an existing type where there is one. Where there is none, define a small named type now, in the file of your own module that owns the concept. A type that belongs in another module is that module's to add, so ask. Then build, type check, or at least load the module, so the plan is checked before any body is written.
4. **Fill the placeholders one at a time.** Each body is either a sequence of calls, written the way step 2 says, or one step. When the next line does not belong to the function's name, cut: name that piece, write the call, and give it a placeholder. Fill it right away or after the function you were in. Either order works.
5. **Check each finished function against its name.** Say what the body does in one sentence. If the sentence needs "and" or "then", or says something the name does not, split the function or rename it. Then apply [The Mixed Function Test](#the-mixed-function-test).

Any one of these is the signal to cut in step 4:

- a second loop, or a block that computes a value the name does not mention;
- a blank line or a comment you want in order to mark "the next part";
- a third level of nesting;
- a rule about the product next to a call to the outside world.

| Language | Placeholder body |
| --- | --- |
| Rust | `todo!()` |
| Go | `panic("not implemented")` |
| Python | `raise NotImplementedError` |
| TypeScript | `throw new Error("not implemented")` |

While the plan stands, a strict build may report that something is unused: a parameter a placeholder does not read, or a function nothing calls yet. Those reports are expected and do not fail the check in step 3. Any other error is a flaw in the plan, so fix it before going on. Never drop a parameter or delete a function to silence an unused report. It goes away when the bodies are written and the top function has its caller.

No placeholder survives the change. Before you report, search every file you changed or created for the marker. One your change added is yours to fill. One that was already there is not part of your change, so leave it. The report lists the functions and types you added and the existing ones you reused, each with its file.

```go
// Step 2: the top function, written first. None of the four functions it calls exists yet.
func denyOversizedEdit(edit Edit, limits Limits, out io.Writer) error {
	existing, err := readTarget(edit.Path)
	if err != nil {
		return err
	}
	addedLines := countAddedLines(edit, existing)
	if !exceedsAllowance(addedLines, limits.Allowance) {
		return nil
	}
	return writeDenial(out, edit.Path, limits)
}

// Step 3: Edit already existed. Limits did not, so it is defined now,
// with only the field the plan needs.
type Limits struct {
	Allowance int
}

// Step 3: typed placeholders. The package builds, so the compiler has checked the plan.
func readTarget(path string) (string, error) { panic("not implemented") }

func countAddedLines(edit Edit, existing string) int { panic("not implemented") }

func exceedsAllowance(addedLines, allowance int) bool { panic("not implemented") }

func writeDenial(out io.Writer, path string, limits Limits) error { panic("not implemented") }
```

## Tiers

Tier a function by what it needs in order to run.

- **Compute.** Needs only its arguments. Branches, calculates, decides. No call to a database, the network, the filesystem, the clock, a random source, or another process. Testable with zero fakes.
- **Adapt.** Needs only its arguments, and its job is shape translation across a boundary: request body to domain type, row to entity, domain error to transport status. Makes no decisions.
- **Orchestrate.** Needs the other functions. Calls them in order and passes results along. It may return early on an error, and it may branch on an answer a called function already decided. It holds no product rule of its own: no limit, no eligibility check, no calculation, no comparison of domain values. Testable with fakes at each seam.
- **Effect.** Needs the outside world. The actual database call, network call, or file read or write, plus turning its failure into the caller's error type, and nothing else.

The placeholders above, filled in:

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
```

`denyOversizedEdit`, the top function above, is the orchestrator. It branches, but only on an error and on the boolean `exceedsAllowance` returned. Writing `addedLines > limits.Allowance` inside it would put the rule in the orchestrator.

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
