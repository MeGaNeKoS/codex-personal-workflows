# Readability, Constants, Control Flow, And Comments

Use when naming values, deciding whether a literal needs a constant, restructuring a branching decision, or writing comments and doc comments. Cross-cutting: applies to any area or language.

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

A two-branch ternary returning a value is fine. The trigger for extraction is multiple branches, validation, fallback, or side effects.

## Comments

**Default to no comment.** A comment is usually a signal that something failed to be expressed in the code itself. Fix the code first.

Before writing a comment, try in this order:

1. a more precise name;
2. a named constant instead of a literal;
3. an extracted, named function for the confusing block;
4. a type that makes the invariant explicit.

If any of those removes the need, do that instead. A reader trusts a name more than a comment, and a name cannot drift out of date.

### The Narrow Exception

Write a comment only when the reason is genuinely unrecoverable from the code **and** losing it would cost real work later. That is rare, and limited to:

| Case | Why code cannot carry it |
| --- | --- |
| An external system forced the shape | the vendor/protocol/browser quirk is not visible here |
| The obvious approach was tried and broke | the absence of that approach is invisible |
| An invariant is enforced somewhere the reader cannot see | the guarantee lives in another module |
| A constant was measured or tuned, not derived | the number's provenance is not in the number |

Keep it to one or two lines. State the fact, not the narration.

```ts
// Wrong: restates the code. Deleting it loses nothing.
// increment the counter
counter += 1

// Wrong: a comment patching a bad name. Rename instead.
// number of minutes before the session expires
const t = 30

// Right: the road not taken, so nobody "simplifies" it back.
// Not Promise.all: S3 throttles above 4 concurrent part uploads per key.
for (const part of parts) await upload(part)
```

### Never

- Restating the statement below it.
- Parameter lists that repeat the signature.
- Section banners, decorative dividers, or step numbering in readable code.
- Attribution, dates, ticket numbers, or changelog entries. Version control owns those.
- Commented-out code. Delete it; history has it.
- Doc comments that only repeat the symbol's name and types.

### Doc Comments

Only on exported symbols whose contract is not obvious from name and signature, and only for what the signature cannot state: a precondition the caller must satisfy, or what happens on failure. Not on every export.

Document the contract, never the implementation, so it survives a rewrite of the body.

### Maintenance

A stale comment is worse than none, because it is trusted. When changing code, update or delete the comments describing it in the same edit. Deleting is usually the right call.
