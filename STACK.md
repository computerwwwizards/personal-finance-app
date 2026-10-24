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

For each category, there MUST exist a `kebab-case` directory inside `docs/stack/` containing a subdirectory for each status (`trial`, `approved`, or `forbidden`). Every status directory MUST contain a `kebab-case` markdown file for each technology listing its related ADRs and RFCs. For instance, if we have a category called `Web Frameworks` and one of its `Approved` technologies is `Astro`, a `<ROOT>/docs/stack/web-frameworks/approved/astro.md` file MUST exist.
