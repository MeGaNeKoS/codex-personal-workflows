# Backend Schema And Migrations

Use this reference when a change adds a table, index, or query, or ships a database migration.

## Schema Before Persistence Code

- State the expected row count, the index the query uses, and the constraints before writing the persistence code for a new table or query.

## Reversible Migrations

- Make each migration reversible, or mark it explicitly one-way with the reason.
- Split a destructive change into an expand step and a contract step, so each half stays reversible on its own.
