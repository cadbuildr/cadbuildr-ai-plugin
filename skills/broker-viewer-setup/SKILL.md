---
name: broker-viewer-setup
description: Set up local broker and connect foundation show() to hub viewer
---

# Rapid iteration on CAD models: Broker + Hub viewer setup and screenshots

You are an AI mechanical engineer that writes CAD as code and wants to iterate rapidly on your CAD models. To do so you can run a local broker once, the viewer page and then take screenshots of your CAD models to iterate rapidely.


## 1. Architecture at a glance

- CAD data stays local:
  - Foundation script -> local broker (`/send`) -> viewer DataChannel.
- Signaling is lightweight and can be local or remote:
  - Offer/answer exchange via `/api/webrtc`.
- You can open viewer on hub.cadbuildr.com/viewer (you might need to ask the user for login for you on his browser). This is also a good way to show your progress in real time to the user.


## 2. Install requirements

- Foundation package available in your Python environment.
- Broker installed:
  - `pip install cadbuildr-broker`

## 3. Start broker

First, check whether a broker is already running:

```bash
curl http://localhost:5050/health
```

If you get a JSON health response, reuse that broker process.

By default, broker runs on `5050`. And you should probably launch it in the background so you can reuse it later.

### Hosted Hub signaling (default)

```bash
cadbuildr-broker &
```

## 4. Run Foundation model and send DAG

By default (`5050`), `show(...)` works out of the box:

```python
from cadbuildr.foundation import show
show(model)
```

If you intentionally run broker on another port, set it in Foundation:

```bash
cadbuildr-broker --broker-port 6060
```

```python
from cadbuildr.foundation import set_port, show
set_port(6060)
show(model)
```

## 5. Take a screenshot from Foundation 

You can use the `get_screenshot_main` function to take a screenshot of your CAD model.

```python
from cadbuildr.foundation.cli import get_screenshot_main

get_screenshot_main([
    "--broker-url",
    "http://localhost:5050",
    "-o",
    "screenshot.png",
])
```

Shell-friendly variant:

```bash
python - <<'PY'
from cadbuildr.foundation.cli import get_screenshot_main
raise SystemExit(get_screenshot_main([
    "--broker-url", "http://localhost:5050",
    "-o", "screenshot.png",
]))
PY
```

## 6. Fast diagnostics

- Broker health:
  - `curl http://localhost:5050/health`
- Common failures:
  - `403` on `localhost:5050` means another app is bound to that port.
  - `ERR_CONNECTION_REFUSED` on signaling URL means viewer is posting offers to an unavailable endpoint.
  - No render update with healthy signaling usually means Foundation points to wrong broker port.

## 7. Starter-template recommendation

In starter-template projects, keep two workflows documented:

- **Interactive local viewer:** `show(...)` + local broker.
- **Cloud export/render:** kernel-api render/export path (no local broker required).


## Analyze screenshots

You should make sure to analyze the screenshots to see if the model geometry is as you expect. When a user asks you for a design by default you should adopt that behavior.
