---
status: "live"
---

# ADR: Offload Non-Toolchain Development Tools to Dedicated Package

## Context and Problem Statement
In a monorepo workspace, CI/CD automated build and testing pipelines require downloading and installing all dependencies declared at the repository root or within application target trees. Including development utilities—such as code scaffolding tools (for example, Plop)—in root `devDependencies` forces CI/CD environments to fetch dependencies that are never executed during automated build or test workflows. However, these tools remain essential as pre-commit guardrails and productivity utilities for human developers and AI agents operating within local environments. We need a strategy to offload non-toolchain development tools from CI/CD environments without losing local developer experience and guardrail capabilities.

## Decision Drivers
* **CI/CD Efficiency & Bandwidth:** CI/CD runners should only download dependencies required for building, testing, linting, and deploying application artifacts, minimizing installation duration and network consumption.
* **Developer & AI Agent Guardrails:** Scaffolding and pre-commit tools must remain version-controlled and available within the repository topology to guide developers and AI agents in maintaining structural consistency.
* **Workspace Isolation & Governance:** Non-toolchain utilities should be encapsulated within a dedicated monorepo workspace package (`packages/non-toolchain-tools`) managed under standard `pnpm` workspace contracts and `.ls-lint.yml` validation rules.

## Considered Options
* **Dedicated Workspace Package (`@computerwwwizard/non-toolchain-tools`)**
* **Root `devDependencies` Co-location**
* **On-Demand Dynamic CLI Execution (for example, `pnpm dlx`)**

## Decision Outcome
Chosen option: "**Dedicated Workspace Package (`@computerwwwizard/non-toolchain-tools`),**" because co-locating non-toolchain utilities (such as Plop) inside a designated workspace package under `packages/non-toolchain-tools` isolates local development tools from target CI/CD pipeline builds, allowing automated workflows to skip fetching non-toolchain packages while preserving version-locked local developer and agent guardrails.

### Benefits
* Good, because CI/CD pipelines can selectively install target workspaces or exclude non-toolchain packages, reducing dependency installation overhead in build/test environments.
* Good, because versioning for scaffolding and local developer utilities remains centralized and explicit within `@computerwwwizard/non-toolchain-tools`.
* Good, because the package structure is enforced via `.ls-lint.yml` rules under `packages/non-toolchain-tools`, maintaining strict filesystem contracts across the repository.

### Trade-offs
* Bad, because developers or AI agents running non-toolchain tools locally must reference the specific workspace package (or run commands via workspace filters) rather than invoking root-level binaries directly.

### Confirmation
Compliance is confirmed by verifying the creation of `packages/non-toolchain-tools/package.json` with package name `@computerwwwizard/non-toolchain-tools`, confirming the removal of `plop` from root `package.json`, and validating filesystem rules in `.ls-lint.yml` via `pnpm dlx @ls-lint/ls-lint`.

## Pros and Cons of the Options

### Dedicated Workspace Package (`@computerwwwizard/non-toolchain-tools`)
Creating a private workspace package at `packages/non-toolchain-tools` that houses development tools not required in production or CI/CD build toolchains.

* Good, because it separates build-critical dependencies from developer/agent helper utilities.
* Good, because `pnpm` workspace filters can bypass non-toolchain packages during lean CI/CD installation steps.
* Bad, because adding a workspace package increases the total number of `package.json` manifests to manage.

### Root `devDependencies` Co-location
Listing all developer tools (including Plop and generator CLI utilities) directly in root `package.json` `devDependencies`.

* Good, because root `devDependencies` binaries are immediately accessible across the monorepo without workspace filtering.
* Bad, because CI/CD build environments are forced to download non-essential scaffolding packages during every pipeline run.

### On-Demand Dynamic CLI Execution (for example, `pnpm dlx`)
Fetching CLI tools dynamically at execution time without adding package dependencies to the monorepo.

* Good, because zero package dependencies are stored in the codebase.
* Bad, because dynamic downloads introduce non-deterministic versioning risks and require external network access for local developer tooling.
