---
status: "live"
---

# ADR: Adopt Stylelint for Cascading Style Sheets (CSS) and Complex Styling Enforcement

## Context and Problem Statement
Maintaining visual consistency, design system tokens, and error-free Cascading Style Sheets (CSS) across a monorepo requires automated static analysis. Modern web development demands strict linting of standard CSS, custom properties, nested selectors, design tokens, and complex preprocessor constructs or mixins.

We evaluated fast unified linters such as Biome as potential alternatives for style linting. However, Biome lacks support for complex styling edge cases, custom preprocessor syntax, and deep nested mixins, and presents significant programmability and extensibility hurdles for custom rule development. We require a mature, battle-tested style linting engine capable of handling complex CSS structures, custom syntax plugins, and deep nested mixins while integrating seamlessly into developer toolchains.

## Decision Drivers
* **Complex CSS & Deep Nested Mixin Support:** The linter must reliably parse and validate advanced CSS features, deeply nested selectors, custom properties, and preprocessor mixins without parser errors.
* **Extensibility & Ecosystem Maturity:** The engine must support a broad ecosystem of community plugins, custom rule extensions, and standard rule presets (`stylelint-config-standard`).
* **Toolchain Offloading:** Scaffolding and developer linting utilities must reside in dedicated non-toolchain packages (`packages/non-toolchain-tools`) to prevent bloating production build environments while preserving local developer guardrails.

## Considered Options
* **Stylelint (`^17.16.0`)**
* **Biome**

## Decision Outcome
Chosen option: "**Stylelint (`^17.16.0`),**" because it provides comprehensive coverage for complex CSS structures, deeply nested mixins, and custom syntax plugins, alongside robust programmability and established default configuration packages (`stylelint-config-standard`).

### Benefits
* Good, because Stylelint natively supports complex CSS features, nested rules, and preprocessor mixin syntax.
* Good, because it integrates with standard rulesets (`stylelint-config-standard`) and offers deep extensibility via custom plugins.
* Good, because non-toolchain developer CLI dependencies can be isolated under `@computerwwwizard/non-toolchain-tools` to optimize CI/CD builds.

### Trade-offs
* Bad, because Stylelint relies on Node.js runtime execution, which may be slower than native Rust-based tooling like Biome for basic CSS files.

### Confirmation
Compliance is verified by running Stylelint against workspace CSS files and ensuring zero linting errors are reported.

## Pros and Cons of the Options

### Stylelint (`^17.16.0`)
A flexible, robust CSS linter that parses modern CSS features, nested selectors, and custom syntax extensions.

* Good, because it fully supports complex CSS cases, deep nested mixins, and custom properties.
* Good, because it has an extensive ecosystem of plugins and standard configuration presets.
* Bad, because Node.js execution speed is slightly lower than native compiled binaries.

### Biome
A fast JavaScript, TypeScript, and CSS linter written in Rust.

* Good, because execution speed is extremely fast due to native compilation.
* Bad, because it lacks support for complex CSS cases, custom preprocessor syntaxes, and deep nested mixins.
* Bad, because extending or programming custom rules is difficult compared to JavaScript-based rule ecosystems.
