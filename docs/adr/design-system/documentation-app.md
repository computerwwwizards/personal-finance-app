---
status: "live"
---

# ADR:  Astro 7.4 as the Static Documentation Compiler for the Decoupled CSS Design System

## Context and Problem Statement
The monorepo architecture separates the core styling library (`packages/design-system`) from its documentation and visual workbench application (`apps/docs`). The styling layer is strictly decoupled—authored as a standalone CSS ruleset utilizing native CSS Cascade Layers (`@layer`) structured around Inverted Triangle CSS (ITCSS) and compiled via Tailwind CSS v4.

The documentation workspace requires a static site generator (SSG) capable of consuming the external styling package across workspace boundaries, rendering mobile-first component views using native browser primitives (such as the HTML Popover API), and executing build-time Markdown/MDX AST transformations without polluting the output DOM with default framework hydration wrappers or mutating the design system's class selectors. Additionally, the architecture must support rich content extensibility via MDX (custom remark/rehype AST plugins, JSX expressions, and custom component mapping) and retain the ability to mount isolated components from external front-end frameworks (React, Vue, Svelte, Solid) via opt-in partial hydration when documenting complex interactive states or testing cross-framework CSS compatibility.

## Decision Drivers
* **Zero Default Client-Side Runtime & Opt-In Multi-Framework Islands:** The compiled output must emit static HTML and CSS with zero mandatory JavaScript runtime bundles (`0 KB` framework execution cost) by default, while supporting isolated framework renderers (`@astrojs/react`, `@astrojs/vue`, `@astrojs/svelte`, `@astrojs/solid-js`) via explicit `client:*` hydration directives.
* **MDX AST Extensibility & Custom Component Injection:** The documentation engine must support `.mdx` authoring via `@astrojs/mdx`, allowing ES module imports, JSX expressions, custom `remark`/`rehype` AST transformation plugins, and automatic mapping of standard Markdown HTML nodes to custom documentation components.
* **DOM and Selector Integrity (Decoupled Styling Isolation):** The build pipeline must consume the external CSS workspace without injecting scoped selector hashes (for example, `[data-v-*]` or `[data-astro-cid-*]`) into documented design system classes or altering the native `@layer` cascade order.
* **Clean `<pre><code>` Text Nodes for CSS Custom Highlight API:** The Markdown/MDX compiler must allow bypassing build-time `<span>` token wrapping so syntax highlighting can rely on single-text-node DOM structures registered via JavaScript `Range` objects and styled via `::highlight()` pseudo-elements.
* **Compile-Time Component Composition & Schema Enforcement:** The documentation engine must support single-source component preview blocks (rendering live HTML alongside escaped code strings via AST slots) and enforce frontmatter data schemas at build time using Zod.
* **Monorepo Cross-Workspace Hot Module Replacement (HMR):** Changes to raw CSS layers inside the external styling workspace must trigger immediate CSS graph invalidation and browser injection inside the documentation workspace without manual rebuild scripts.

## Considered Options
* **[Astro 7.4](https://github.com/withastro/astro/releases "Releases · withastro/astro")** (`output: 'static'` with `@astrojs/mdx`, multi-framework renderer support, and Vite 8 / Rolldown workspace resolution)
* **[Eleventy (11ty)](https://11ty.dev "Eleventy official documentation")** (`markdown-it` pipeline with Liquid/WebC shortcodes)
* **[Zola](https://getzola.org "Zola static site generator")** (Rust single-binary SSG with Tera templates)
* **[Storybook HTML](https://js.org "Storybook official site")** (`@storybook/html-vite` component workbench)

## Decision Outcome
Chosen option: "**[Astro 7.4](https://github.com/withastro/astro/releases "Releases · withastro/astro"),**" because its Rust-accelerated `.astro` and MDX compilation pipeline alongside Vite 8 natively resolves cross-workspace CSS exports with instant HMR, strips all component wrappers down to zero-JS static HTML at build time, provides rich MDX AST extensibility via [`@astrojs/mdx`](https://docs.astro.build/en/guides/integrations-guide/mdx/ "Astro MDX integration documentation"), and allows mounting React, Vue, Svelte, or Solid components inside [Astro's Front-End Framework Islands](https://docs.astro.build/en/guides/framework-components/ "Astro Front-end frameworks documentation") without shipping their JavaScript runtimes unless explicitly hydrated.

### Benefits
* Good, because [`@astrojs/mdx`](https://docs.astro.build/en/guides/integrations-guide/mdx/ "Astro MDX integration documentation") compiles `.mdx` files through a unified `remark`/`rehype` AST pipeline, enabling custom Markdown element overrides (`export const components = { pre: CustomPre }`), inline JSX data bindings, and direct imports of `.astro` or UI framework components inside documentation prose.
* Good, because Astro's multi-framework islands architecture supports importing React (`.tsx`), Vue (`.vue`), Svelte (`.svelte`), or Solid components directly into `.astro` or `.mdx` files—rendering them to static HTML by default to verify decoupled CSS class interoperability across frameworks, or hydrating specific interactive playgrounds via `client:load` / `client:visible`.
* Good, because `.astro` component slots (`Astro.slots.render('default')`) allow reading the raw HTML buffer at build time to render both the live component canvas and the unmutated `<pre><code>` block from a single declaration inside MDX without duplicating markup.
* Good, because importing the decoupled styling workspace at the layout frontmatter boundary passes the stylesheet directly through Vite's CSS pipeline without applying Astro's scoped `[data-astro-cid-*]` attribute transformation to the design system selectors.
* Good, because Zod schemas in `src/content.config.ts` validate both `.md` and `.mdx` frontmatter (`glob({ pattern: "**/*.{md,mdx}" })`) during AST parsing, halting the build if required component metadata is missing.

### Trade-offs
* Bad, because developers must explicitly guard against using unscoped `<style>` blocks inside `.astro` or framework UI shell components; unlayered component styles have higher cascade precedence than `@layer utilities` and `@layer components` in the decoupled CSS library unless isolated.
* Bad, because mixing multiple JSX-based UI frameworks (such as React and Preact/Solid) in the same workspace requires explicit file-extension or directory scoping (`include`/`exclude` arrays) in `astro.config.mjs` so the compiler dispatches JSX AST nodes to the correct framework renderer.
* Bad, because props passed to hydrated framework components (`client:*`) cross a server-to-client serialization boundary, prohibiting non-serializable values like functions or class instances from being passed directly from `.astro` or `.mdx` files.

### Confirmation
Compliance with this architectural decision is enforced via four automated gates in CI and workspace configuration:
1. **Static & MDX Configuration Gate (`astro.config.mjs`):** A CI check asserts that `output: 'static'`, `integrations: [mdx()]`, and `markdown: { syntaxHighlight: false }` are explicitly configured, ensuring MDX inherits disabled syntax highlighting and preventing accidental Shiki `<span>` DOM pollution.
2. **Client Hydration Budget Audit:** A static analysis check scans `.astro` and `.mdx` files for `client:*` directives (`client:load`, `client:idle`, `client:visible`, `client:only`), failing PRs if an external UI framework runtime (React/Vue/Svelte) is hydrated on core pure-CSS documentation routes without an explicit exemption tag.
3. **Post-Build DOM & Bundle Audit:** A post-build validation script scans `apps/docs/dist/**/*.html` to verify:
   * Zero `<span class="line">` or inline-styled syntax tokens exist inside `<pre><code>` blocks.
   * Zero `[data-astro-cid-*]` attributes appear inside `<div data-demo-canvas>` preview containers.
   * Zero unexpected framework hydration scripts (`<astro-island>`) exist on baseline CSS primitive pages.
4. **Workspace Boundary Check (`dependency-cruiser`):** Enforces one-way dependency flow where `apps/docs` may import CSS entrypoints from `packages/design-system`, but `packages/design-system` has zero dependency on Astro, MDX, or UI framework packages.

## Pros and Cons of the Options

### Astro 7.4
An islands-architecture static site generator built on Vite 8 (Rolldown) with a Rust-accelerated `.astro` and Markdown/MDX compiler that renders `.astro`, `.md`, `.mdx`, and multi-framework UI components (React, Vue, Svelte, Solid) into static HTML at build time while stripping framework runtime code by default.

* Good, because [`@astrojs/mdx`](https://docs.astro.build/en/guides/integrations-guide/mdx/ "Astro MDX integration documentation") extends standard Markdown with JSX expressions, ES module `import`/`export` statements, YAML/TOML frontmatter, and custom `remark`/`rehype` AST transformers while compiling down to zero-JS HTML.
* Good, because official framework integrations (`@astrojs/react`, `@astrojs/vue`, `@astrojs/svelte`, `@astrojs/solid-js`) allow embedding third-party framework components directly inside `.astro` layouts or `.mdx` documentation pages, rendering them as zero-JS static HTML by default or selectively hydrating them via `client:*` directives.
* Good, because Vite's module graph resolves symlinked monorepo workspaces natively, enabling instant CSS HMR when editing `@layer` rules in the decoupled styling package.
* Good, because `Astro.slots.render()` provides direct programmatic access to the rendered HTML buffer during SSG compilation, enabling single-source live previews and raw code blocks inside MDX components.
