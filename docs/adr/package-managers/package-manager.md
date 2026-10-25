---
status: "live"
---

# ADR: Use pnpm Workspaces for Monorepo Package Management and Direct Module Sharing

## Context and Problem Statement
Sharing internal modules across multiple applications in the repository typically requires an explicit compilation step (`build`) for each internal package before consuming applications can resolve updated exports. This introduces build-pipeline overhead and slows down local development feedback loops when modifying shared code across boundaries.

## Considered Options
* pnpm Workspaces (using the `workspace:*` protocol with raw source exports)
* npm Workspaces
* Yarn Workspaces

## Decision Outcome
Chosen option: "pnpm Workspaces (using the `workspace:*` protocol with raw source exports)," because the `workspace:` protocol links internal packages via hard links and symlinks in `node_modules`, allowing consuming applications to directly import and bundle TypeScript/JavaScript source entry points without running an intermediate package build step.

### Benefits
* Good, because `workspace:*` resolves strictly to local workspace packages, preventing accidental resolution from the remote npm registry and eliminating the need for pre-building internal modules during local development.
* Good, because content-addressable storage deduplicates dependencies across packages on disk via hard links.

### Trade-offs
* Bad, because consuming applications must configure their bundlers or runtimes (for example, Vite, Next.js `transpilePackages`, or `tsconfig.json` path mappings) to compile unbuilt TypeScript source files from linked workspace packages directly.
* Bad, because strict `node_modules` symlink isolation prevents undeclared phantom dependencies from resolving, requiring explicit dependency declarations in each package's `package.json`.
