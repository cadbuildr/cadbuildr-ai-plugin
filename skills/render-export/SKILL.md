---
name: render-export
description: How to use foundation and the kernel-api to render and export meshes
---

# Render and export (local-first)

Turn a Foundation-generated DAG into STL/STEP/mesh with the managed CADBuildr kernel-api.

Default path is **local Python + `KernelApiClient`**.  
Use MCP `render_dag` only when direct Python execution is unavailable or explicitly requested.

If you want live viewer updates with `show(...)` instead of export, use `cadbuildr://foundation/broker-viewer-setup`.

## 1. Default path: use `KernelApiClient` directly

Use Foundation helpers to generate canonical DAG, then render in one local flow.

```python
from pathlib import Path

from cadbuildr.foundation.dag_utils import show_dag
from cadbuildr.foundation.coms.kernel_api import KernelApiClient

dag = show_dag(model_obj)
Path("assets").mkdir(exist_ok=True)

client = KernelApiClient()
try:
    stl_bytes = client.download_stl(
        dag=dag,
        kernel="replicad",
        file_name="model.stl",
    )
finally:
    client.close()

Path("assets/model.stl").write_bytes(stl_bytes)
```

Why this is preferred:
- No huge DAG JSON in conversation/tool args.
- Binary bytes are returned directly for STL/STEP exports.
- Single execution path, less agent drift and less token churn.

## 2. DAG contract (for both direct and MCP modes)

- DAG must be a JSON object with keys: `version`, `rootNodeId`, `DAG`, `serializableNodes`.
- Field semantics:
  - `DAG`: node-id -> node payload map.
  - `serializableNodes`: type-name -> type-id registry map.
- Preferred generator: `show_dag(model_obj)` (avoid hand-built DAG payloads).

## 3. MCP fallback mode (only when needed)

If local Python execution is not available, use MCP tool `render_dag`:
- Pass `dag` as object/dict (never `json.dumps(dag)`).
- Use `kernel="replicad"`.
- Use `format="stl"` by default for file export.
- For `stl`/`step`, decode `contentBase64` and write bytes to `assets/`.

## 4. Anti-patterns

- Do not create ad-hoc render scripts unless user asked for reusable automation.
- Do not print/paste full DAG JSON in chat.
- Do not print full base64 payloads.
- Do not bounce between MCP and direct HTTP in the same flow unless user asks.
- Do not keep validating payload files after a successful render call.

## 5. Plan-validate-execute loop

- **Plan:** pick one mode (direct local default, MCP fallback).
- **Validate:** ensure DAG has required keys and `rootNodeId in DAG`.
- **Execute:** run one render call.
- **Done condition:** exported bytes written under `assets/`.
- **Escalation:** after two failed attempts, stop and report exact error + one next action.

## 6. Endpoint policy

- Kernel rendering/export must use managed kernel-api (no localhost/`127.0.0.1` fallback hacks).
- Canonical endpoint contract remains `POST /v1/kernels/:kernel/render` with `format=json|stl|step`.
- Preferred base URL env for direct mode is `CAD_COMPILE_API_BASE_URL`.

This flow is model-agnostic (table, chair, hammer, bracket, and others); only model code and file names change.
