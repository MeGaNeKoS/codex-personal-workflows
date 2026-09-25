# Frontend Responsibility Splits

Use when splitting one owner across modules or components while keeping that owner. Moving code to a *different* owner is [Promotion](./promotion.md). Naming the resulting files is [Naming And File Structure](./naming-and-file-structure.md).

## When To Split

Split when one file owns independently changing responsibilities, boundary code and core behavior, unrelated state transitions, or multiple test seams.

- Preserve the same owner when extracting private modules.
- Keep orchestration at the owner entry point and move cohesive details behind narrow local contracts.
- Do not split simple markup or deterministic helpers when indirection would hide behavior without creating a stable responsibility.
- File length, directory size, and file count are review signals only. They do not authorize a split by themselves. The file growth rule, including the line threshold, what it gates, and where a reworked function moves, is [Core Practices](../../core.md); this branch does not restate it.

## Useful View Split Points

```text
shell            versus content
list             versus detail
form workflow    versus field group
toolbar          versus data view
overlay          versus parent workflow
```

Each of these separates things that change for different reasons, which is what makes the seam stable.

## After Splitting

Verify that public exports, imports, state ownership, and test seams still reveal the original owner. A split that makes the owner harder to identify has moved cost rather than removed it.

**Also load:** [Testing](./testing.md) when a split creates, moves, or changes a verification seam.
