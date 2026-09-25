# Frontend Naming And File Structure

Use after ownership is decided, when naming files and directories. Splitting one owner across modules is [Responsibility Splits](./responsibility-splits.md). Repository and framework filename conventions take precedence over everything here.

## Names And Paths

Let the containing path communicate ownership.

```text
Redundant   features/users/users-client.ts
Right       features/users/client.ts          path already says "users"

Qualified   features/users/grpc-client.ts     distinguishes protocol
            features/billing/stripe-client.ts distinguishes external system
            features/orders/server-state.ts   distinguishes runtime
```

Keep a qualifier when it distinguishes a protocol, external system, public contract, runtime, sibling role, or coexisting implementation. Drop it when the path already carries the meaning.

- Name application modules by application role, feature files by product responsibility, and neutral UI by compositional role.
- A generic filename such as `types.ts`, `client.ts`, or `index.ts` is acceptable only when its containing path makes the owner and role unambiguous.
- Create a directory for a named responsibility or enforced boundary, not to satisfy a universal tree or file-count target.
- Do not create root-level `components`, `services`, `types`, `utils`, `helpers`, `common`, or `shared` directories without a precise owner contract.
