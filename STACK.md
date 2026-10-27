# Stack

The current document is a catalog that keeps track of our decisions about the use of some technologies by category.

New decisions about tech-stack MUST be planned first.

Readers MUST NOT always lock in using a stack from this catalog. If a listed tool does not offer a solution for your use case, and you have exhausted every possibility and evaluated the trade-offs, consider opening an RFC/ADR draft. 

The structure of this document MUST be:

```md
## Category name
Optional brief explanation (280 chars max)

### Trial

|name|version|notes|link|
|---|---|---|---|

### Approved

|name|version|notes|link|
|---|---|---|---|

### Forbidden

|name|version|notes|link|
|---|---|---|---|
```
MUST use semver.

For each category, there MUST exist a `kebab-case` directory inside `docs/stack/` containing a subdirectory for each status (`trial`, `approved`, or `forbidden`). Every status directory MUST contain a `kebab-case` markdown file for each technology listing its related ADRs and RFCs. For instance, if we have a category called `Web Frameworks` and one of its `Approved` technologies is `Astro`, a `<ROOT>/docs/stack/web-frameworks/approved/astro.md` file MUST exist.

## Web Frameworks
Frameworks used to construct and compile our web applications and site experiences.

### Trial

|name|version|notes|link|
|---|---|---|---|

### Approved

|name|version|notes|link|
|---|---|---|---|
|Astro|7.4|Static site compiler for apps and docs|[`astro.md`](./docs/stack/web-frameworks/approved/astro.md)|

### Forbidden

|name|version|notes|link|
|---|---|---|---|

## Web Styling
Styling solutions, CSS frameworks, and design tokens tooling for styling applications.

### Trial

|name|version|notes|link|
|---|---|---|---|

### Approved

|name|version|notes|link|
|---|---|---|---|
|Stylelint|^17.16.0|Mighty CSS linter for standard, custom, and nested style rules|[`stylelint.md`](./docs/stack/web-styling/approved/stylelint.md)|
|Tailwind CSS|^4|Utility CSS engine coupled with native CSS cascade layers|[`tailwind-css.md`](./docs/stack/web-styling/approved/tailwind-css.md)|

### Forbidden

|name|version|notes|link|
|---|---|---|---|

## Package Managers
Tooling used to manage monorepo dependencies and workspaces.

### Trial

|name|version|notes|link|
|---|---|---|---|

### Approved

|name|version|notes|link|
|---|---|---|---|
|pnpm|^12.14.0|Fast, disk-space efficient package manager with workspace isolation|[`pnpm.md`](./docs/stack/package-managers/approved/pnpm.md)|

### Forbidden

|name|version|notes|link|
|---|---|---|---|

## Folder Structure
Tools and contracts for validating monorepo directory topology and naming conventions.

### Trial

|name|version|notes|link|
|---|---|---|---|

### Approved

|name|version|notes|link|
|---|---|---|---|
|ls-lint|^2|Toolchain-agnostic filesystem layout linter|[`ls-lint.md`](./docs/stack/folder-structure/approved/ls-lint.md)|

### Forbidden

|name|version|notes|link|
|---|---|---|---|

## Prose Linters
Tools and frameworks used for static analysis, linting, and style enforcement of natural language prose and technical documentation.

### Trial

|name|version|notes|link|
|---|---|---|---|

### Approved

|name|version|notes|link|
|---|---|---|---|
|Vale|3.14.1||[`vale.md`](./docs/stack/prose-linters/approved/vale.md)|

### Forbidden

|name|version|notes|link|
|---|---|---|---|

## Runtime
JavaScript runtime environments used to execute workspace applications and scripts.

### Trial

|name|version|notes|link|
|---|---|---|---|

### Approved

|name|version|notes|link|
|---|---|---|---|
|Node.js|^26|Standard JavaScript runtime with built-in permission model|[`node-js.md`](./docs/stack/runtime/approved/node-js.md)|

### Forbidden

|name|version|notes|link|
|---|---|---|---|

## Boilerplate Utilities
Tools and utilities used for scaffolding and generating consistent workspace templates and code assets.

### Trial

|name|version|notes|link|
|---|---|---|---|
|plop|^4.0.5|Micro-generator framework for scaffolding consistent code components and assets|[`plop.md`](./docs/stack/boilerplate-utilities/trial/plop.md)|

### Approved

|name|version|notes|link|
|---|---|---|---|

### Forbidden

|name|version|notes|link|
|---|---|---|---|

## Build Tools
Tools and bundlers used to serve development servers and compile web application assets.

### Trial

|name|version|notes|link|
|---|---|---|---|

### Approved

|name|version|notes|link|
|---|---|---|---|
|Vite|^8.0.0|Frontend build tool supporting complex bundling and multi-page application dev servers|[`vite.md`](./docs/stack/build-tools/approved/vite.md)|

### Forbidden

|name|version|notes|link|
|---|---|---|---|
