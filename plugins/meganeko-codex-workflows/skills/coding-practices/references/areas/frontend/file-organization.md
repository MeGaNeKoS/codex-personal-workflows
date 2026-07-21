# Frontend File Organization

Use this reference after ownership is decided, when naming files/directories or splitting one owner across modules or components. Repository and framework filename conventions take precedence.

## Names And Paths

- Let the containing path communicate ownership. Prefer `features/users/client.ts` to `features/users/users-client.ts` when only one client role exists.
- Keep a qualifier when it distinguishes a protocol, external system, public contract, runtime, sibling role, or coexisting implementation, such as `grpc-client.ts`, `stripe-client.ts`, or `server-state.ts`.
- Name application modules by application role, feature files by product responsibility, and neutral UI by compositional role.
- A generic filename such as `types.ts`, `client.ts`, or `index.ts` is acceptable only when its containing path makes the owner and role unambiguous.
- Create a directory for a named responsibility or enforced boundary, not to satisfy a universal tree or file-count target.
- Do not create root-level `components`, `services`, `types`, `utils`, `helpers`, `common`, or `shared` directories without a precise owner contract.

## Responsibility Splits

- Split when one file owns independently changing responsibilities, boundary code and core behavior, unrelated state transitions, or multiple test seams.
- Preserve the same owner when extracting private modules. Moving code to a broader owner is promotion and requires the component/promotion policy.
- Keep orchestration at the owner entry point and move cohesive details behind narrow local contracts.
- For view components, useful split points include shell versus content, list versus detail, form workflow versus field group, toolbar versus data view, and overlay versus parent workflow.
- Do not split simple markup or deterministic helpers when indirection would hide behavior without creating a stable responsibility.
- File length, directory size, and file count are review signals only. They do not authorize a split, directory, or promotion by themselves.

After a split, verify that public exports, imports, state ownership, and test seams still reveal the original owner.

**Also load:** [Ownership And Dependencies](./ownership-and-dependencies.md) and [Components And Promotion](./components-and-promotion.md) when an extraction changes owners rather than preserving the current owner.

**Also load:** [Testing](./testing.md) when a split creates, moves, or changes a verification seam.
