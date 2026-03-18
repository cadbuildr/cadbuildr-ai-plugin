---
name: foundation-setup
description: Helps getting you started with a CADBuildr project that uses the foundation lib
---

# CADBuildr Foundation setup (local authoring)

Use this when you need to create or work with a CAD model using the CADBuildr Foundation stack locally.

## 1. Environment

- **Python**: 3.10+ with `pip` or `uv` available.
- **Foundation package**: Install the CADBuildr Foundation Python package from PyPI:
  - `pip install cadbuildr-foundation`
  - The package provides the Foundation API (parts, sketches, operations).

## 2. Kernel (CAD engine)

Foundation can target different kernels. Commonly used:

- **replicad**: Browser/WASM-friendly; good for local runs and MCP render. Foundation uses the canonical kernel route `POST /v1/kernels/:kernel/render`.
- Direct compile base URL should be configured via `CAD_COMPILE_API_BASE_URL`.

## 3. Minimal project layout

- **Single file**: One Python file (e.g. `model.py`) that defines and exports a DAG or part is enough to start.
- **Package**: For larger projects, use a package with a clear entry point that produces a DAG (e.g. a function that returns the result of your model and, if needed, converts it to the DAG format expected by the render API).

## Starter template

You can also use the github template to start a new project: https://github.com/cadbuildr/starter-template

For starter-template local interactive viewing, install and run the broker:

- `pip install cadbuildr-broker`
- `cadbuildr-broker`

By default, broker runs on port `5050`, which avoids common collisions on `5000`.

## 4. DAG format (for MCP render)

The MCP `render_dag` tool expects a JSON object with:

- `version`: schema version (e.g. `1`).
- `rootNodeId`: id of the root node in the DAG.
- `DAG`: node id -> node payload map.
- `serializableNodes`: type-name -> type-id registry map.

Your Foundation code typically builds a part/assembly; use Foundation DAG helpers to convert to this exact format (for example `show_dag(...)`) instead of hand-building the payload.

## 5. Next steps

- Read `cadbuildr://foundation/cheatsheet` for how to structure model code.
- Read `cadbuildr://foundation/broker-viewer-setup` for local  fast iterations using `show(...)` with broker + viewer + screenshots.
- Read `cadbuildr://foundation/render-export` for using MCP to render and save STL/STEP.
