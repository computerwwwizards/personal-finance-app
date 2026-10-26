---
status: "live"
---

# ADR: Offload Non-toolchain Development Tools to Dedicated Workspace Package

## Context and Problem Statement
In automated continuous integration and continuous deployment (CI/CD) environments, downloading unnecessary development dependencies increases installation times, bloats runner disk footprints, and consumes extra network bandwidth. Many development tools—such as scaffolding generators like Plop (`docs/adr/boilerplate-utilities/plop-template-generation.md`) or local pre-commit guardrails—are essential for human developers and AI agents authoring code, but are not required for core build, compilation, or deployment toolchain tasks in CI/CD pipelines. We need a structured strategy to isolate non-toolchain development tools from root production and build configurations.

## Decision Drivers
* **CI/CD Pipeline Optimization:** Avoid downloading and installing packages in production or build pipelines when those tools do not participate in building, testing, or deploying application artifacts.
* **Developer and Agent Guardrails:** Preserve local interactive tools, generators, and pre-commit checks required for developer productivity and AI agent automation.
* **Workspace Isolation:** Leverage monorepo workspace features to group auxiliary development utilities cleanly within the `packages` directory structure.
* **Strict Repository Governance:** Maintain full compliance with repository structure validation via `ls-lint` (`docs/adr/folder-structure/folder-structure-manifest.md`) and prose standards via Vale (`docs/adr/documentation/prose-linting.md`, `docs/adr/documentation/vale-base-style.md`).

## Considered Options
* **Dedicated Workspace Package (`packages/non-toolchain-tools`)**
* **Root `package.json` `devDependencies` Bundle**

## Decision Outcome
Chosen option: "**Dedicated Workspace Package (`packages/non-toolchain-tools`),**" because creating a separate private package inside `packages/non-toolchain-tools` allows non-toolchain development dependencies—such as `plop`—to be housed independently. This offloads non-essential tools from CI/CD environments while retaining guardrails and scaffolding capabilities for developers and AI agents in local workspace environments.

### Benefits
* Good, because CI/CD build matrix jobs can skip installing non-toolchain packages, reducing dependency installation overhead and pipeline duration.
* Good, because developer tools like `plop` remain fully accessible inside the monorepo workspace for local generation and scaffolding workflows.
* Good, because isolating non-toolchain packages establishes a explicit boundary between core toolchain dependencies and local guardrail utilities.

### Trade-offs
* Bad, because managing a dedicated workspace package requires maintaining an additional `package.json` and updating monorepo filesystem rules in `.ls-lint.yml`.
* Bad, because executing scripts across non-toolchain packages requires running commands via `pnpm --filter` or workspace-aware package manager invocations.

### Confirmation
Compliance is confirmed by verifying the presence of `packages/non-toolchain-tools/package.json` with `plop` listed as a dependency, checking that `plop` is omitted from root `package.json`, ensuring `.ls-lint.yml` validates `non-toolchain-tools`, and confirming prose linting compliance via Vale during pre-commit checks.

## Pros and Cons of the Options

### Dedicated Workspace Package (`packages/non-toolchain-tools`)
A dedicated private workspace package inside `packages/non-toolchain-tools` containing non-toolchain development dependencies and local guardrail configurations.

* Good, because it prevents non-toolchain dependencies from polluting root application configurations or CI/CD build caches.
* Good, because it keeps developer guardrails and scaffolding generators available for local human and AI agent workflows.
* Neutral, because it integrates natively with `pnpm` monorepo workspace filtering.
* Bad, because developers must run workspace commands targeting the package when invoking non-toolchain tools directly.

### Root `package.json` `devDependencies` Bundle
Keeping all local scaffolding generators, pre-commit utilities, and developer guardrail dependencies inside the root `package.json` file.

* Good, because all development tools are available globally across the repository without targeting specific workspace packages.
* Bad, because CI/CD pipelines must download and install all root `devDependencies` during build jobs, increasing build times and bandwidth usage.
* Bad, because it blurs the line between critical build toolchain tools and optional developer scaffolding utilities.
