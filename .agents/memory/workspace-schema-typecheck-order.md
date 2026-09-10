---
name: Workspace schema typecheck order
description: How project-reference declarations affect API typechecks after database schema edits.
---

Run the workspace library build before relying on an artifact-level TypeScript check after changing or merging database schema code.

**Why:** The API consumes the database package through TypeScript project references. Its declaration output can be stale even when the schema source already contains the required columns, producing misleading "property does not exist" errors.

**How to apply:** Run `pnpm run typecheck:libs` first, then run the focused artifact typecheck. Treat any remaining errors as current application errors rather than stale referenced declarations.