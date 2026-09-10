---
name: API schema version compatibility
description: OpenAPI integer schemas currently generate zod.int(), which is incompatible with the workspace's installed Zod version.
---

Use numeric schemas for generated API counts and identifiers until the Orval/Zod versions are aligned.

**Why:** Code generation succeeds but the chained library typecheck fails when generated validation calls `zod.int()` against the installed Zod API.

**How to apply:** If the workspace upgrades Zod or Orval, re-check generated output and restore integer constraints only after `pnpm --filter @workspace/api-spec run codegen` and the library typecheck both pass.