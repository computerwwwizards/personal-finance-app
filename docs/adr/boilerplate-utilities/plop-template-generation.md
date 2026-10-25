---
status: "live"
---

# ADR: Adopt Plop ^4.0.5 for Codebase Template Generation (Trial)

## Context and Problem Statement
As the repository grows, developers and automated processes frequently scaffold new components, packages, and workspace structures. Manually copying existing files or crafting bespoke bash scripts leads to structural drift, inconsistent boilerplate, missing required metadata, and increased onboarding cognitive load. We need a lightweight, deterministic micro-generator framework that enforces standard templates and prompt-driven file generation across the monorepo while remaining in a Trial evaluation phase.

## Decision Drivers
* **Consistency & Governance:** Standardize boilerplate generation for new modules, components, and packages without relying on ad-hoc manual file copying.
* **Developer Experience:** Provide interactive CLI prompts and declarative template handlebars for prompt-driven scaffolding.
* **Monorepo Integration:** Maintain zero footprint outside workspace dependencies by adopting a simple JavaScript/Node-native generator tool.
* **Trial Flexibility:** Evaluate real-world adoption during the Trial phase before locking in long-term tooling or considering alternatives like Hygen or Baker.

## Considered Options
* **Plop (`^4.0.5`)**
* **Manual Creation via Bash Scripting**

## Decision Outcome
Chosen option: "**Plop (`^4.0.5`),**" under **Trial** status. Plop provides a lightweight micro-generator framework powered by Handlebars templates and Inquirer prompts, eliminating error-prone manual scripting while allowing us to evaluate its fit during the trial period.

### Benefits
* Good, because template creation is declarative and uses Handlebars syntax, making generators easy to author and maintain.
* Good, because interactive CLI prompts guide developers through required template variables, ensuring consistent structure across all generated files.
* Good, because adoption under Trial status leaves room to evaluate alternatives (such as Baker or Hygen) if specialized generator capabilities are required in the future.

### Trade-offs
* Bad, because adding generator dependencies requires maintaining `plopfile.js` configurations alongside workspace source code.
* Bad, because custom helper logic in Handlebars templates can become complex if generators grow beyond simple file scaffolding.

### Confirmation
Compliance is confirmed by inspecting `package.json` `devDependencies` for `plop: "^4.0.5"`, registering the tool under `Trial` in `STACK.md`, and validating the existence of the decision record in `docs/adr/boilerplate-utilities/plop-template-generation.md`.

## Pros and Cons of the Options

### Plop (`^4.0.5`)
A small, focused CLI tool that pairs Handlebars template files with Inquirer prompt flows to generate or modify workspace code files.

* Good, because it requires minimal boilerplate setup via a single `plopfile.js`.
* Good, because it integrates natively with Node.js and standard package managers.
* Neutral, because it is currently evaluated under Trial status.
* Bad, because advanced generators with complex conditional file transformations may require custom Handlebars helpers.

### Manual Creation via Bash Scripting
Scaffolding files using shell scripts (`mkdir`, `cp`, `sed`) or raw filesystem manipulation.

* Good, because it requires no external npm package dependencies.
* Bad, because bash scripts are fragile, non-portable across operating systems without POSIX guarantees, and difficult to maintain as templates evolve.
* Bad, because providing interactive user prompts or multi-file template substitutions in raw shell scripts requires verbose, error-prone code.
