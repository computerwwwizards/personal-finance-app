---
status: "live"
---

# ADR: Adopt Vale v3.14.1 for AST-Aware Prose and Specification Linting

## Context and Problem Statement
ADRs, RFCs, and system specifications are executable contracts; ambiguity and structural decay degrade system integrity (specially for AI agents creating the code) just as severely as uncompilable source code. However, enforcing prose quality inside technical repositories fails when the linting engine treats structured markup (Markdown, AsciiDoc, MDX, and inline source-code docstrings) as flat character arrays. 

Without syntactic and lexical boundaries, naive linters parse fenced code blocks, inline type signatures, URI paths, and YAML frontmatter as natural language prose—generating false-positive cascades that force engineers to disable verification gates. Furthermore, writing and maintaining custom prose rules from scratch is an unforced operational tax when industry-standard technical writing guidelines already exist. We require a deterministic, syntax-aware static analysis engine pinned to an immutable release artifact that provides built-in linting out of the box and natively composes industry-grade rulesets (`Google`, `Microsoft`, `RedHat`) without requiring a foreign language runtime.

## Decision Drivers
* **Turnkey & Industry-Grade Ruleset Composition:** The engine must include a built-in default ruleset (`Vale`) out of the box and natively ingest battle-tested upstream engineering style guides (`Google`, `Microsoft`, `RedHat`, `write-good`, `alex`) via declarative configuration, eliminating the need to author or maintain custom rule definitions.
* **Syntactic AST & Markup Scoping:** The engine must construct an Abstract Syntax Tree (AST) of the host document (Markdown, HTML, TSX/TS docstrings via Tree-sitter) to programmatically isolate prose scopes (`heading`, `paragraph`, `table.cell`) and strip code spans, fenced blocks, and mathematical expressions prior to rule evaluation.
* **Zero Foreign Runtime Dependency:** The linter must distribute as a self-contained, statically compiled binary with deterministic SHA-256 checksums, avoiding Python virtual environments (`pip`/`venv`) or C-toolchains in CI and local developer environments.
* **Sub-Second Concurrent Execution:** Linting across workspace specifications must execute concurrently across CPU cores, completing in milliseconds to remain viable for pre-commit hooks and real-time Language Server Protocol (LSP) editor diagnostics.

## Considered Options
* **Vale (`v3.14.1`)**
* **proselint**
* **GNU diction / style (`style`)**

## Decision Outcome
Chosen option: "**Vale (`v3.14.1`),**" because it combines AST-driven markup pruning and a zero-dependency Go binary with turnkey support for the built-in `Vale` style and declarative composition of industry-standard packages (`Google`, `Microsoft`) via `vale sync`.

### Benefits
* Good, because engineers get immediate enforcement using the built-in `Vale` style and upstream `Google` or `Microsoft` developer documentation packages without authoring a single custom rule.
* Good, because fenced code blocks, inline monospaced tokens, and URL targets are pruned at the AST parser level before upstream rules execute, reducing false positives on technical identifiers to near zero.
* Good, because multiple upstream styles can be stacked and selectively tuned in `.vale.ini` (`BasedOnStyles = Vale, Google, Microsoft`), allowing teams to inherit industry standards while disabling noisy individual rules in one line.

### Trade-offs
* Bad, because hydrating external style packages (`Google`, `Microsoft`) requires running `vale sync` over the network during initial environment setup or CI initialization unless the fetched `StylesPath` directory is committed or cached.
* Bad, because upstream rulesets relying on part-of-speech (POS) tagging heuristics occasionally flag overloaded domain terminology, requiring minor additions to a flat-file vocabulary list (`accept.txt`).

### Confirmation
Compliance is enforced via three deterministic verification gates:
1. **Binary & Version Pinning:** CI pipelines and local dev toolchains must verify `vale --version` resolves strictly to `v3.14.1` (Linux x86_64 archive SHA-256: `c0d29c9f9f6ad9fd97c132db2dcce0fa5efd1cf7d3e3f6e4bc3ca5fd4d77ff7d`).
2. **Package Hydration Gate:** CI pipelines cache `.vale/styles` and execute `vale sync` to deterministically pull pinned upstream packages (`Google`, `Microsoft`) defined in `.vale.ini`.
3. **CI Static Analysis Gate:** Pull requests modifying `**/*.md`, `**/*.mdx`, or specification directories must execute `vale --minAlertLevel=error --no-wrap` and return exit code `0` before merge.

## Pros and Cons of the Options

### Vale (`v3.14.1`)
A syntax-aware, concurrent prose linter written in Go (`github.com/vale-cli/vale/v3`) that parses markup formats into ASTs and applies both a built-in default ruleset (`Vale`) and composable upstream style packages hosted in the official `vale-cli` package registry.

* Good, because it ships with the built-in `Vale` style (spelling, repeated words, capitalization) out of the box and supports one-line installation of official `Google`, `Microsoft`, `RedHat`, and `write-good` rulesets via `Packages`.
* Good, because it parses Markdown, MDX, HTML, and source-code comments (`.ts`, `.tsx`, `.rs`, `.go`) into scoped AST nodes, completely bypassing code blocks and literal tokens.
* Good, because it compiles to a single static Go binary (`v3.14.1`) that lints file trees concurrently across CPU cores without requiring an external language runtime.
* Neutral, because it evaluates heuristic style and lexical topology rather than deep grammatical subject-verb agreement.
* Bad, because pulling external style packages listed in `Packages` requires network I/O via `vale sync` unless cached or vendored into the repository.

### proselint
A Python-based CLI prose linter that aggregates stylistic advice from literary editors (Garner, Strunk & White, Orwell) into hardcoded Python regular-expression modules evaluated over raw text buffers.

* Good, because its built-in checks work out of the box for literary prose without initial configuration.
* Neutral, because it outputs standard Unix `line:column` or JSON diagnostics compatible with generic linter aggregators.
* Bad, because its hardcoded rules target literary essays rather than software engineering specifications, lacking support for technical style standards like the Google or Microsoft developer documentation guides.
* Bad, because it lacks a markup AST parser; it scans raw character streams with regular expressions, causing inline code snippets, CLI flags, and URLs in technical specs to trigger massive false-positive cascades.
* Bad, because it requires provisioning a Python runtime and dependency tree, executing single-threaded regex passes that scale poorly.

### GNU diction / style (`style`)
A legacy C utility originating from AT&T UNIX (bundled in GNU `diction`) that computes surface-level readability indices (Flesch-Kincaid, Coleman-Liau, ARI) and word-usage warnings over plain-text streams.

* Good, because it is compiled in C with a negligible memory footprint and computes mathematical readability formulas deterministically out of the box.
* Bad, because it cannot ingest or compose modern industry style guides (`Google`, `Microsoft`), limiting checks to archaic UNIX phrase tables and syllable math.
* Bad, because it is completely blind to Markdown, HTML, and code syntax; it ingests YAML frontmatter, ASCII tables, and TypeScript code blocks as contiguous English sentences, corrupting all readability metrics.
* Bad, because it dumps aggregate statistical summaries rather than actionable, token-positioned (`line:col`) diagnostic alerts with severity levels for CI gating.
