# OpenJEV support

This fork adds optional [OpenJEV](https://openjev.sh) support alongside
TypeSafe. TypeSafe remains the default; anyone with a TypeSafe key sees zero
behaviour change.

## What was added

| File | Change |
|---|---|
| `src/rev/client.py` | `OPENJEV_URL`, `OPENJEV_MODEL` constants; `Client.openjev()` classmethod (key defaults to `$OPENJEV_API_KEY`); docstring updated. Default model stays `jev-latest` (TypeSafe). |
| `bench/jevbench.py` | `--openjev` flag alongside `--jev` — measures `api.openjev.sh` using `Client.openjev()`. |
| `bench/speed.py` | `--openjev` flag alongside `--jev` — same. |
| `skills/rev/references/building.md` | OpenJEV paragraph next to the TypeSafe one; 503 added to the error-status list. |
| `README.md` | OpenJEV support note after the intro; `Client.openjev()` in the Python example. |

No TypeSafe code, defaults, or documentation was removed or renamed.

## Provider selection

The `Client` class is endpoint-agnostic: you pass the URL, key, and model id.
TypeSafe is the default (`model="jev-latest"`). OpenJEV is opt-in:

1. **Explicit choice wins** — `Client.openjev(key=...)` or
   `Client("https://api.openjev.sh", key=..., model="openjev")`.
2. **TypeSafe if its key is set** — `Client("https://api.typesafe.ai", key=...)`
   with `model="jev-latest"` (the default). Unchanged.
3. **OpenJEV if only `OPENJEV_API_KEY` is set** — `Client.openjev()` reads it
   from the environment.

The bench scripts mirror this: `--jev` targets TypeSafe (key from
`~/typesafe.txt`), `--openjev` targets OpenJEV (key from `$OPENJEV_API_KEY`).

## Configuration

```bash
export OPENJEV_API_KEY=...    # from https://openjev.sh/dashboard
```

```python
from rev import Client
jev = Client.openjev()         # uses $OPENJEV_API_KEY
jev.noul("Is this spam?", "Does the text look like spam?")
```

## Verification

A live POST to `https://api.openjev.sh/v1/systemone` with model `openjev`,
state `ping`, and one `noul` question returned HTTP 200 with a valid answer.
No repository code was executed. A grep confirmed no hardcoded
`api.typesafe.ai` default was introduced — the original TypeSafe references in
docstrings, examples, and bench scripts remain unchanged.

## Upstream

Original project: https://github.com/54yyyu/rev by @54yyyu (MIT License).
