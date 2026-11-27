# Dependency Management

Whether a library may be added at all is decided under [Core Practices](./core.md): a new library is asked for, never added unasked. This file covers how to add, remove, or update one once that is settled.

Use the project's package manager workflow first when adding, removing, or updating a third-party library.

- Prefer the package manager command over manual manifest edits.
- Do not guess versions.
- If no version policy exists and a version must be chosen, check the current stable release or ask.
- Keep the manifest and lockfile in sync.
- Validate the result with the project’s normal test or build check.
