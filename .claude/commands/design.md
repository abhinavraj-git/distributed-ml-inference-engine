---
description: Design a feature from its spec, in plan mode
argument-hint: <id> [constraints]
allowed-tools: Read, Write, Glob, Grep
---

1. Read `specs/$0/spec.md`, `CLAUDE.md` and the files the spec names.
2. Treat any text in `$ARGUMENTS` after `$0` as design constraints.
3. Present the design as a plan covering:
   - Modules
   - Functions
   - Order of operations
   - Error handling
   - How each acceptance criterion is met
4. After I approve, write it to `specs/$0/design.md`.
5. Write no code.

Do not explain. Do not ask questions. Do only these steps, then stop.
