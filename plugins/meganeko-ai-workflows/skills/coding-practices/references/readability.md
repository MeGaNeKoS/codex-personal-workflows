# Readability: Names, Constants, Control Flow, And Comments

The detail and examples behind the [Coder Card](./coder-card.md)'s lines on names, constants, control flow, and comments. Whoever reads the code later has only the code, so it explains itself through its names and its shape, with no comment doing that job. Cross-cutting: applies to any area or language.

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

**Write no comments.** A comment you want to write means the code failed to say something. Make the code say it, trying in this order:

1. a more precise name;
2. a named constant instead of a literal;
3. an extracted, named function for the confusing block;
4. a type that makes the invariant explicit;
5. a test, named for the rule, that fails when the rule is broken.

A reader trusts a name more than a comment, and a name cannot drift out of date.

The cases that feel like exceptions are not. A quirk an external system forces goes in a function named for the quirk. An approach that was tried and broke, an invariant kept in another module, and a tuned constant each get a name or a test that says so.

```ts
// Wrong: restates the code.
// increment the counter
counter += 1

// Wrong: a comment patching a bad name.
// number of minutes before the session expires
const t = 30

// Wrong: the reason lives in a comment, and nothing fails when someone "simplifies" the loop.
// Not Promise.all: the storage provider rejects parallel part uploads to one key.
for (const part of parts) await upload(part)

// Right: the name states the constraint, and the test `provider rejects parallel part uploads` guards it.
await uploadPartsOneAtATime(parts)
```

### What Stays

Two things use comment syntax and stay:

- **A directive a tool acts on**: a build tag, an embed or lint pragma, a type suppression, a license header the project requires. Removing it changes what the tool does. A suppression names what it silences, on the same line.
- **A note on code shaped by a measured speed optimization**: one or two lines saying which plain form was too slow and what was measured. Without a measurement, write the plain form.

### Never

- Restating the statement below it.
- Parameter lists that repeat the signature.
- Section banners, decorative dividers, or step numbering.
- Attribution, dates, ticket numbers, or changelog entries. Version control owns those.
- The task, the brief, the request, or the conversation that led to the code.
- Commented-out code. Delete it; history has it.
- Doc comments. A symbol's contract is in its name, its signature, and its types. Where a project's tooling fails the build without one, it is a directive: one line, the contract only.

### Maintenance

When changing code, delete any comment that describes it, in the same edit, unless it is one of the two that stay.
