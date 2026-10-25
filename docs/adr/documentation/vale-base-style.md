---
status: "live"
---

# ADR: Adopt Google Style Guide as Base Prose Style for Vale

## Context and Problem Statement
Following our adoption of Vale for AST-aware prose linting (`docs/adr/documentation/prose-linting.md`), we need to establish a foundational base style guide for technical prose, specifications, and architecture decision records. Clear and deterministic stylistic guidelines are vital not only for human engineers but also for AI agents generating and parsing technical documentation. We evaluated leading industry style guides—specifically Google and Microsoft—to determine the optimal base ruleset.

## Decision Drivers
* **AI Compatibility & Technical Precision:** The selected style guide must prioritize technical clarity, exact terminology, and structural rigor, optimizing comprehension and generation accuracy for AI agents.
* **Developer & API Focus:** The ruleset must target technical software engineering documentation, API references, and system design specifications rather than general consumer software UI text.
* **Turnkey Vale Integration:** The style guide must integrate natively with Vale via official style packages without requiring custom rule creation.

## Considered Options
* **Google Developer Documentation Style Guide**
* **Microsoft Writing Style Guide**

## Decision Outcome
Chosen option: "**Google Developer Documentation Style Guide,**" because it provides highly technical, structured, and precise guidelines tailored for developer-facing documentation and technical specifications. In contrast, evaluation of the Microsoft Writing Style Guide revealed it is oriented toward conversational, consumer-facing software and UI copy, making it less technical and less effective for AI agents operating on software engineering codebases.

### Benefits
* Good, because Google's technical focus emphasizes concise, imperative, and structured prose, which significantly improves prompt accuracy and text parsing for AI language models.
* Good, because Vale natively supports the official `Google` style package, allowing automated enforcement via `vale sync` out of the box.
* Good, because developer-centric conventions reduce ambiguity in architecture specifications, code comments, and system design documents.

### Trade-offs
* Bad, because Google's strict style rules can feel overly rigid for casual or non-technical documentation.
* Bad, because enforcing upstream Google rules may require periodically updating repository dictionary files (`accept.txt`) for custom technical jargon.

### Confirmation
Compliance is enforced alongside Vale v3.14.1 (`docs/adr/documentation/prose-linting.md`) using the `Google` ruleset package in `.vale.ini` during pre-commit checks and CI pull request gates.

## Pros and Cons of the Options

### Google Developer Documentation Style Guide
A style guide created by Google designed specifically for software developers, API specifications, and technical architecture documentation.

* Good, because it is purpose-built for highly technical developer documentation, code samples, and system specifications.
* Good, because its precise grammatical rules and objective tone eliminate ambiguity for AI agents reading or writing technical content.
* Good, because an officially maintained, turnkey Vale style package (`Google`) is available directly via `vale sync`.
* Bad, because its strict rules may require an adjustment period for team members used to informal technical writing.

### Microsoft Writing Style Guide
A style guide published by Microsoft focusing on clear, conversational, and human-centered writing for general applications and end-user documentation.

* Good, because it excels at establishing a friendly, conversational voice for consumer-facing interfaces and product user guides.
* Neutral, because it is an established industry reference for general software user experience writing.
* Bad, because it prioritizes a conversational tone over strict technical precision, making it less technical and poorly suited for AI agents parsing complex technical specifications.
* Bad, because its broader, less technical guidelines introduce stylistic variance that degrades AI model output consistency.
