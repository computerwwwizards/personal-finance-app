---
status: "live"
---

# ADR: Adopt Vite 8 as the Standard Build Tool for Complex Bundling and Multi-Page Applications

## Context and Problem Statement
As workspace applications expand across web applications and documentation tooling, we require a fast, modern build tool and development server capable of handling complex module bundling as well as serving simple HTML multi-page applications (MPA). The build system must deliver rapid hot module replacement (HMR), efficient dependency pre-bundling, and flexible multi-page entry point routing.

## Decision Drivers
* **Complex Bundling & Performance:** Fast module resolution, tree-shaking, and optimized production bundling for JavaScript, TypeScript, and modern asset graphs.
* **Multi-Page Application (MPA) Support:** Seamless development server capabilities for serving simple HTML multi-page application structures alongside complex single-page or hybrid apps.
* **Developer Experience & Ecosystem Standards:** Instant local startup times, rapid hot module replacement (HMR), and standardized plugin ecosystem integration.

## Considered Options
* **Vite (`^8.0.0`)**

Note: Vite 8 was evaluated as the sole option for workspace build tooling, and no other options were considered.

## Decision Outcome
Chosen option: "**Vite (`^8.0.0`),**" because it provides native ES module-based development serving with fast HMR, robust multi-page application (MPA) support, and optimized production bundling tailored for complex workspace asset graphs.

### Benefits
* Good, because native ES module dev serving offers near-instantaneous startup times regardless of application size.
* Good, because built-in multi-page application (MPA) configuration simplifies serving multiple HTML entry points during development and production builds.
* Good, because advanced Rollup/Rolldown production bundling handles complex dependency graphs, code splitting, and asset optimization seamlessly.

### Trade-offs
* Bad, because non-ESM legacy dependencies may require additional plugin configuration or pre-bundling overrides.

### Confirmation
Compliance is confirmed by registering Vite `^8.0.0` under Approved build tools in `STACK.md`, maintaining the stack reference documentation at `docs/stack/build-tools/approved/vite.md`, and enforcing project build configurations.

## Pros and Cons of the Options

### Vite (`^8.0.0`)
A modern, native ES module-powered build tool and development server.

* Good, because it delivers extremely fast development server startup and HMR.
* Good, because it natively supports both complex module bundling and simple multi-page application HTML entry points.
* Bad, because legacy CommonJS-only modules require explicit pre-bundling handling.
