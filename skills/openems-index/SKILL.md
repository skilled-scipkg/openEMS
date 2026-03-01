---
name: openems-index
description: This skill should be used when users ask how to use openems and the correct generated documentation skill must be selected before going deeper into source code.
---

# openems Skills Index

## Route the request
- Classify the request into one of the generated topic skills listed below.
- Prefer abstract, workflow-level guidance for large scientific packages; do not attempt full function-by-function coverage unless explicitly requested.

## Generated topic skills
- `openems-getting-started`: Getting Started (initial setup, quickstarts, and core concepts)
- `openems-api-and-scripting`: API and Scripting (language bindings, APIs, and programmatic interfaces)

## Documentation-first inputs
- `python/doc`

## Tutorials and examples roots
- `matlab/Tutorials`
- `matlab/examples`
- `python/Tutorials`
- `python/doc/Tutorials`

## Test roots for behavior checks
- `TESTSUITE`
- `.github/smoketests`
- `python/Tests`

## Escalate only when needed
- Start from topic skill primary references.
- For Getting Started requests, escalate through `skills/openems-getting-started/references/doc_map.md` then `skills/openems-getting-started/references/source_map.md`.
- For API/build requests, escalate through `skills/openems-api-and-scripting/references/doc_map.md` then `skills/openems-api-and-scripting/references/source_map.md`.
- Use targeted symbol search while inspecting source (e.g., `rg -n "<symbol_or_keyword>" Analyse Common FDTD cmake matlab nf2ff tools python/openEMS`).

## Source directories for deeper inspection
- `Analyse`
- `Common`
- `FDTD`
- `cmake`
- `matlab`
- `nf2ff`
- `tools`
- `python/openEMS`
