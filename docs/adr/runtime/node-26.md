---
status: "live"
---

# ADR: Adopt Node.js 26 as the Standard JavaScript Runtime

## Context and Problem Statement
JavaScript runtime choices impact security posture, runtime performance, and system stability across our applications and workspace environments. In modern server and CLI environments, executing untrusted code or dependencies without explicit security controls poses a significant security risk. We need a secure, standardized, and actively maintained JavaScript runtime.

We evaluated modern runtime options, specifically Node.js 26 and Deno, to determine the ideal standard runtime for our applications and workflows.

## Decision Drivers
* **Security & Permission Model:** The runtime must enforce a granular permission model that restricts access to system resources, specifically disabling network access by default unless explicitly permitted.
* **Long-Term Maintainability & Ecosystem Support:** The runtime must be actively maintained with standard long-term support and broad ecosystem compatibility.
* **Ecosystem Compatibility:** Compatibility with existing Node.js modules, tools, and build pipelines without needing custom adapter layers.

## Considered Options
* **Node.js 26**
* **Deno**

## Decision Outcome
Chosen option: "**Node.js 26,**" because Node.js 26 offers an integrated permission model that restricts capabilities by default—disallowing network access by default—while maintaining full backward compatibility with the Node.js ecosystem. In comparison, Deno was evaluated but is officially not maintained anymore.

### Benefits
* Good, because Node.js 26 includes a built-in permission model that disallows network access by default, reducing the attack surface against supply chain vulnerabilities and untrusted code execution.
* Good, because adopting Node.js 26 maintains complete compatibility with the existing Node.js package ecosystem, tooling, and infrastructure.
* Good, because Node.js provides a clear maintenance lifecycle and robust enterprise backing.

### Trade-offs
* Bad, because runtime flags must be configured when network or host resource access is explicitly required by an application or tool.

## Pros and Cons of the Options

### Node.js 26
The standard JavaScript runtime environment featuring an enhanced permission model.

* Good, because it provides built-in runtime permission flags that deny network access by default.
* Good, because it provides seamless compatibility with existing Node.js dependencies and tooling.
* Bad, because enabling permissions for complex legacy modules may require explicit flag management.

### Deno
A modern JavaScript/TypeScript runtime with built-in security features.

* Good, because it was designed with an explicit permission system.
* Bad, because Deno is officially not maintained anymore.
* Bad, because module resolution differences and ecosystem divergence introduce migration overhead compared to native Node.js environments.
