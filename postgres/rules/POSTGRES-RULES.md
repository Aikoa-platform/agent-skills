---
name: Postgres Skill Rules
---

# Postgres Skill Rules

- Default to read-only behavior and keep `POSTGRES_READ_ONLY` set to "true" unless the user explicitly requests writes.
- Do not run destructive statements (DROP, TRUNCATE, DELETE without a WHERE clause) unless the user explicitly requests them.
- Never log secrets such as connection URLs, passwords, or raw `.env` values.
- Prefer `--file` for multi-statement queries and migrations.
- If a query fails, include the error output and ask for the next step.
