# Readability: Names, Constants, Control Flow, And Comments

Load before any code change. Whoever reads the code later has only the code, so it explains itself through its names and its shape, with no comment doing that job. Cross-cutting: applies to any area or language.

## Names

The name is the explanation. Test every name you write or touch: cover the body and read only the name and its type or signature. If you cannot say what the value holds, or what the function returns and changes, rename it.

- **Say what it is, in the domain's words.** `overdueInvoices`, not `data`, `result`, `value`, `tmp`, `info`, `obj`, or `list2`. Name a thing by its role in the problem, not by its type or container: `recipients`, not `emailArray` or `strList`.
- **A function starts with a verb and names its one result or effect**: `parseLimits`, `countNewlines`, `sendReceipt`. A name that needs "and" (`validateAndSave`) is two functions. Split it.
- **No verb that hides the work.** `handle`, `process`, `manage`, `do`, and `execute` say nothing about what happens. Use the verb for what the function does: `rejectExpiredToken`, not `handleToken`. An entry point that only dispatches (`main`, `run`, a framework's `handler`) is the exception.
- **A name tells the truth about effects.** `get`, `find`, `is`, `has`, `compute`, `to`, and `parse` promise that nothing is written, sent, or deleted. A function that does any of those says so: `saveOrder`, `publishEvent`, `deleteDraft`.
- **A boolean reads as a yes or no question**: `isExpired`, `hasItems`, `canRetry`, `shouldNotify`. State it in the positive: `isEnabled`, not `isNotDisabled`.
- **A quantity carries its unit**: `timeoutMs`, `sizeBytes`, `priceCents`, `retryDelaySeconds`. A bare `timeout` sends the reader elsewhere for the unit.
- **No abbreviation the domain does not use.** `request`, `configuration`, `index`, not `req`, `cfg`, `idx`, unless the surrounding code already uses the short form throughout. A loop index `i` and the language's conventional short names (`err`, `ctx`, `self`) are fine.
- **One word per concept.** If the module says `fetch` for a network read, do not also say `get`, `load`, and `retrieve` for the same kind of thing. Reuse the word already in use.
- **Length follows scope.** A variable that lives for three lines can be short. An exported name, a module level name, and anything used far from its definition is spelled out.
- **A type or module is a noun naming one responsibility**: `LimitsFile`, `ReceiptMailer`. `Manager`, `Helper`, `Util`, `Data`, and `Info` name no responsibility. The area branches own file and directory naming.
- **A test is named for the behavior and the condition**: `rejects_expired_token`, `TestParseLimitsIgnoresWrongType`. `test1`, `TestHandler`, and `it works` name neither.

Repository conventions come first. Match the casing, vocabulary, and word order of the code around you. Where a language, a framework, or an interface fixes a name's form, that form wins over a rule here: a Go getter carries no `Get`, a Rust conversion is `as_`, `to_`, or `into_`, a React component is a noun in PascalCase, and a method an interface requires keeps the interface's name (`String`, `Error`).

```ts
// Wrong: the reader must open every body to learn what these are.
const d = await get(id)
if (check(d)) process(d, true)

// Right: the call site reads as a sentence.
const invoice = await fetchInvoice(invoiceId)
if (isOverdue(invoice)) sendReminder(invoice)
```

## Named Values

The rule is about *ownership*, not about banning literals.

```ts
// Wrong: the rule is invisible and duplicated at every call site.
if (attempts > 3) return
setTimeout(retry, 30000)

// Right: the constant lives with the rule it expresses.
// upload/upload.constants.ts
export const UPLOAD_MAX_ATTEMPTS = 3
export const UPLOAD_RETRY_DELAY_MS = 30_000

// Still right: self-evident arithmetic stays inline.
const midpoint = Math.floor(items.length / 2)
const [first] = items
```

Ask "whose rule is this?" If the answer names an owner, the value belongs to that owner as a constant. If there is no rule, it is arithmetic and stays inline.

Name thresholds, sizes, timeouts, retry counts, breakpoints, layers, and protocol values. Keep each constant with the rule's owner, never in a global constants dump.

## Control Flow

- **Return early.** Handle the failure and the simple case first, and return. The main path stays at the lowest indent, and no `else` follows a `return`.
- **Two levels of nesting at most** inside a function. A third level is a function waiting for a name.
- **Three or more outcomes get a named function.** So does any branch that validates, falls back, or has a side effect. A conditional expression with two outcomes stays inline, where it reads at a glance.
- **No boolean parameter that switches behavior.** Write two named functions or a named option: `renderCompact(report)` and `renderFull(report)`, not `render(report, true)`. A call site reads without opening the definition.
- **More than three parameters become one named structure**: a struct or options object with named fields, so a call site cannot swap two values unnoticed.

```ts
// Wrong: decision order is buried in nesting, and no branch is testable alone.
const tier = user.isStaff ? 'staff' : user.plan === 'pro'
  ? (user.trialEndsAt ? 'pro-trial' : 'pro')
  : user.plan === 'team' ? 'team' : 'free'

// Right: a named resolver. Each rule is inspectable and independently testable.
function resolveTier(user: User): Tier {
  if (user.isStaff) return 'staff'
  if (user.plan === 'team') return 'team'
  if (user.plan === 'pro') return user.trialEndsAt ? 'pro-trial' : 'pro'
  return 'free'
}
```

```go
// Wrong: the success path sits three levels deep.
func chargeOrder(order Order) error {
	if !order.IsPaid {
		if order.Total > 0 {
			if err := charge(order); err == nil {
				return markPaid(order)
			} else {
				return err
			}
		}
	}
	return nil
}

// Right: each exit is stated once, the main path reads top to bottom,
// and the rule has a name of its own.
func chargeOrder(order Order) error {
	if !needsCharge(order) {
		return nil
	}
	if err := charge(order); err != nil {
		return err
	}
	return markPaid(order)
}

func needsCharge(order Order) bool {
	return !order.IsPaid && order.Total > 0
}
```

## Comments

**Default to no comment.** A comment is usually a signal that something failed to be expressed in the code itself. Fix the code first.

Before writing a comment, try in this order:

1. a more precise name;
2. a named constant instead of a literal;
3. an extracted, named function for the confusing block;
4. a type that makes the invariant explicit.

If any of those removes the need, do that instead. A reader trusts a name more than a comment, and a name cannot drift out of date.

### The Narrow Exception

Write a comment only for a case in this table. The test: if a reader with only the code could answer "why is it this way", delete the comment.

| Case | Why code cannot carry it |
| --- | --- |
| An external system forced the shape | the vendor, protocol, or browser quirk is not visible here |
| The obvious approach was tried and broke | the absence of that approach is invisible |
| An invariant is enforced somewhere the reader cannot see | the guarantee lives in another module |
| A constant was measured or tuned, not derived | the number's provenance is not in the number |
| A tool reads it: a lint or type suppression, a build tag, a license header | the tool needs it in place |

Keep it to one or two lines. State the fact, not the narration. A suppression names what it silences and why, on the same line.

```ts
// Wrong: restates the code. Deleting it loses nothing.
// increment the counter
counter += 1

// Wrong: a comment patching a bad name. Rename instead.
// number of minutes before the session expires
const t = 30

// Right: the road not taken, so nobody "simplifies" it back.
// Not Promise.all: the storage provider rejects parallel part uploads to one key.
for (const part of parts) await upload(part)
```

### Never

- Restating the statement below it.
- Parameter lists that repeat the signature.
- Section banners, decorative dividers, or step numbering in readable code.
- Attribution, dates, ticket numbers, or changelog entries. Version control owns those.
- The task, the brief, the request, or the conversation that led to the code.
- Commented-out code. Delete it; history has it.
- Doc comments that only repeat the symbol's name and types.

### Doc Comments

Only on exported symbols whose contract is not obvious from name and signature, and only for what the signature cannot state: a precondition the caller must satisfy, or what happens on failure. Not on every export.

Document the contract, never the implementation, so it survives a rewrite of the body.

### Maintenance

A stale comment is worse than none, because it is trusted. When changing code, update or delete the comments describing it in the same edit. Deleting is usually the right call.
