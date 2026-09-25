# Frontend Promotion

Use when moving any frontend responsibility to a broader owner: components, contracts, state, clients, or runtime-neutral code. Deciding which owner a *new* component belongs to is [Component Ownership](./component-ownership.md).

## Conditions

Promote only when **all** hold:

1. the destination is a repository-designated owner or an explicitly approved neutral primitive family;
2. the destination contract matches the behavior and dependencies;
3. a separate production consumer exists, or neutral ownership is a recorded product architecture decision;
4. feature policy stays outside the promoted API;
5. dependency direction remains valid; and
6. the public API does not expose former-owner internals.

## Non-Evidence

```text
Not promotion evidence:
  two files look similar                -> similarity is not shared ownership
  the file is large                     -> size is a review signal, not promotion
                                            evidence
  the filename is generic               -> a name is not an owner
  the import repeats                    -> duplication is cheaper than a wrong owner
  another consumer may appear           -> predicted reuse is not a consumer
  tests use it in two places            -> tests are not production consumers
```

Leaving code feature-local is reversible. Untangling a wrongly-shared owner is not, because every later consumer bends the contract further toward its own needs.

Where new code goes is governed by [Core Practices](../../core.md), not by this table.

Update ownership/import checks and tests when promotion changes a public boundary.
