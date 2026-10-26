---
status: "live"
---

# ADR: Use `ls-lint` (`.ls-lint.yml`) for Toolchain-Agnostic Monorepo Filesystem Layout Validation

## Context and Problem Statement
In a multi-package `pnpm` monorepo, maintaining a consistent directory topology, enforcing file/folder naming conventions across mixed extensions (for example, `PascalCase` for `.tsx` components and `kebab-case` for `.ts` utilities), and preventing undeclared root-level directories require automated structural validation. Without a machine-readable filesystem contract, workspaces drift in directory layout, unauthorized root directories proliferate, and automated tooling or AI agents lack a deterministic blueprint of the repository's physical structure.

## Decision Drivers
* **Toolchain and Language Agnosticism:** The validation engine must operate independently of the JavaScript Abstract Syntax Tree (AST)/linter pipeline (ESLint) so the monorepo can adopt alternative toolchains (such as Biome or Oxlint) or non-TypeScript workspaces (Go, Rust, Python) without breaking filesystem checks.
* **Hierarchical Rule Inheritance:** Subdirectories and individual workspace packages must automatically inherit baseline root rules while supporting localized overrides for specific subtrees (`apps/*`, `packages/*/src/components`).
* **Co-located Extension Casing Primitives:** The specification format must support multiple casing conventions (`kebab-case`, `PascalCase`, `camelCase`) mapped per file extension within the same directory without requiring complex raw regular expressions for standard naming styles.
* **Strict Directory Allowlisting & Existence Checks:** The format must be capable of locking top-level directory creation (`apps`, `packages`), requiring mandatory workspace entry points (`exists:1`), and blocking forbidden file patterns (`exists:0`).
* **Low Readability & Maintenance Overhead:** The manifest must avoid deeply nested JSON bracket bloat and require zero custom parser maintenance inside the repository.

## Considered Options
* **`ls-lint` (`.ls-lint.yml`)**
* **`eslint-plugin-project-structure` (`projectStructure.json` / TypeScript Config)**
* **Custom Declarative JSON/YAML Manifest with Node.js/Vitest Validator**

## Decision Outcome
Chosen option: "**`ls-lint` (`.ls-lint.yml`),**" because it provides an actively maintained, standalone Go binary (distributed via `@ls-lint/ls-lint`) that validates directory trees, cascading extension casing rules, and file existence constraints in milliseconds without coupling repository structure enforcement to ESLint or requiring custom validation code.

### Benefits
* Good, because execution is decoupled from Node.js AST parsers, enabling zero-friction migration from ESLint to Biome or Oxlint and supporting polyglot directories natively.
* Good, because the YAML hierarchy under the `ls:` key mirrors the physical directory tree, cascading parent rules downward and supporting glob-based workspace scopes (`packages/*/src`) and multi-config composition (`--config`).
* Good, because full-repository filesystem scans execute in ~5–15 ms via a compiled Go binary rather than booting a Node.js linting runtime.

### Trade-offs
* Bad, because restricting a directory to a strict allowlist of subfolder names requires overriding `.dir` using pipe-separated exact matches (`.dir: regex:apps | regex:packages`) and explicitly resetting `.dir: kebab-case` in child directories to prevent the allowlist from cascading downward.
* Bad, because `ls-lint` validates the physical filesystem state on disk rather than providing real-time IDE red-squiggles inside open code buffers prior to file creation.

### Confirmation
Compliance is enforced by defining `.ls-lint.yml` at the repository root and executing `pnpm dlx @ls-lint/ls-lint` (or `pnpm ls-lint` via `@ls-lint/ls-lint` in root `devDependencies`) as a mandatory step in the CI pipeline and git `pre-commit` hook. Any exit code non-zero blocks pull request merges.

## Pros and Cons of the Options

### `ls-lint` (`.ls-lint.yml`)
A standalone, cross-platform filesystem linter written in Go and configured via a hierarchical `.ls-lint.yml` manifest at the repository root (with optional per-package config overlays).

* Good, because it maps file extensions (`.ts`, `.tsx`, `.css`, `.dir`) directly to built-in naming primitives (`kebab-case`, `PascalCase`, `camelCase`, `snake_case`) and existence constraints (`exists:0`, `exists:1`) inside the same directory scope.
* Good, because it has zero dependency on the JavaScript compiler or linter toolchain, executing as a static binary via npm wrapper (`@ls-lint/ls-lint`).
* Neutral, because it ignores untracked build artifacts only when explicitly listed under the `ignore:` array (for example, `node_modules`, `dist`, `.git`).
* Bad, because cascading `.dir` rules require child directories to explicitly redefine `.dir` whenever the parent directory uses an exact-match allowlist (`regex:apps | regex:packages`).

### `eslint-plugin-project-structure` (`projectStructure.json` / TypeScript Config)
An ESLint plugin that validates the repository file tree against a strict JSON Schema or TypeScript configuration object during the ESLint execution lifecycle.

* Good, because its tree schema treats any undeclared file or directory as a validation error by default without needing explicit `.dir` resets in child nodes.
* Good, because it surfaces structural violations directly inside the IDE via the ESLint language server extension.
* Bad, because it tightly couples filesystem layout validation to ESLint, preventing clean adoption of Biome or Oxlint and requiring dummy parser configurations to inspect non-JS/TS assets.
* Bad, because representing deep monorepo trees in JSON Schema requires verbose nested `{ "name": "...", "children": [...] }` objects that degrade human readability.

### Custom Declarative JSON/YAML Manifest with Node.js/Vitest Validator
An in-house schema (`layout.json` or `layout.yaml`) paired with a custom recursive directory walker implemented in TypeScript and executed via Vitest or Node.js.

* Good, because the schema syntax and inheritance semantics can be tailored to exact workspace preferences without third-party tool constraints.
* Neutral, because it uses standard Node.js `fs` and `glob` primitives already present in the monorepo test runner.
* Bad, because designing a custom JSON/YAML syntax that cleanly separates directory structure from node metadata (`strict`, `optional`, `casing`, `forbidden`) either introduces verbose `@meta` wrapper objects or requires maintaining a custom Domain-Specific Language (DSL) parser.
* Bad, because the engineering team must maintain the validation engine, error reporting, and glob resolution logic internally rather than relying on an established open-source tool.
