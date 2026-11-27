# Backend Schema And Migrations

Use this reference when a change adds a table, index, or query, or ships a database migration.

## Schema Before Persistence Code

- Before writing persistence code for a new table or query, know the expected row count, the index the query uses, and the constraints. Take them from the task or the existing schema. Where they are not stated, ask; a new table, column, or index is a decision.

## Reversible Migrations

- Make each migration reversible, or mark it explicitly one-way with the reason.
- Split a destructive change into an expand step and a contract step, so each half stays reversible on its own.
