# Stack

The current document is a catalog that keeps track of our decisions about the use of some technologies by category.

New decisions about tech-stack MUST be planned first.

Readers MUST NOT always lock in using a stack from this catalog. If a listed tool does not offer a solution for your use case, and you have exhausted every possibility and evaluated the trade-offs, consider opening an RFC/ADR draft. 

The structure of this document MUST be:

```md
## Category name
Optional brief explanation (280 chars max)

### Trial

|name|version|notes|
|---|---|---|

### Approved

|name|version|notes|
|---|---|---|

### Forbidden

|name|version|notes|
|---|---|---|
```
MUST use semver.

For each category, there MUST exist a `kebab-case` directory inside `docs/stack/` containing a subdirectory for each status (`trial`, `approved`, or `forbidden`). Every status directory MUST contain a `kebab-case` markdown file for each technology listing its related ADRs and RFCs. For instance, if we have a category called `Web Frameworks` and one of its `Approved` technologies is `Astro`, a `<ROOT>/docs/stack/web-frameworks/approved/astro.md` file MUST exist.

## Web Frameworks
Frameworks used to construct and compile our web applications and site experiences.

### Trial

|name|version|notes|
|---|---|---|

### Approved

|name|version|notes|
|---|---|---|
|Astro|7.4|Static site compiler for apps and docs|

### Forbidden

|name|version|notes|
|---|---|---|

## Web Styling
Styling solutions, CSS frameworks, and design tokens tooling for styling applications.

### Trial

|name|version|notes|
|---|---|---|

### Approved

|name|version|notes|
|---|---|---|
|Tailwind CSS|^4|Utility CSS engine coupled with native CSS cascade layers|

### Forbidden

|name|version|notes|
|---|---|---|

## Package Managers
Tooling used to manage monorepo dependencies and workspaces.

### Trial

|name|version|notes|
|---|---|---|

### Approved

|name|version|notes|
|---|---|---|
|pnpm|^12.14.0|Fast, disk-space efficient package manager with workspace isolation|

### Forbidden

|name|version|notes|
|---|---|---|

## Folder Structure
Tools and contracts for validating monorepo directory topology and naming conventions.

### Trial

|name|version|notes|
|---|---|---|

### Approved

|name|version|notes|
|---|---|---|
|ls-lint|^2|Toolchain-agnostic filesystem layout linter|

### Forbidden

|name|version|notes|
|---|---|---|
