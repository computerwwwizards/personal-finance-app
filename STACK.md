# Stack

The current document is a catalog that keeps track of our decisions about the use of some technologies by category.

New decisions about tech-stack MUST be planed first.

Readers MUST NOT always lock in using stack from this catalog, if a tool that is not listed does not  offer a solution for your use case and you have exhausted every possibility and evalauted the trade offs, consider opening a RFC/ADR draft. 

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

For each category MUST exist a directory containing a directory for each status (trial, approved or forbidden), and every one of this directories contain for each technology a markdown file listing related ADRs and RFCs. For instance if we have a category called `Web Frameworks` and one of its `Approved` technologies  is `Astro`, a `<ROOT>/docs/stack/approved/astro.md` MUST exist.   

