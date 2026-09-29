# pod-testapi

The `testapi` candy of the OpenCharly candy library, as a standalone repo (the
candy de-submodule cutover, kind-prefixed naming). It provides a FastAPI test
service answering HTTP on port 9090 under supervisord.

## What it provides

Installs `fastapi` + `uvicorn` into a pixi environment and copies `app.py` (a
FastAPI app exposing `GET /` and `GET /health`) into the service working
directory. supervisord runs `uvicorn app:app` on `0.0.0.0:9090`, so the running
container answers HTTP 200 on `/` (`{"status":"ok"}`) and `/health`
(`{"healthy":true}`).

| Property | Value |
|---|---|
| Requires | `layer-supervisord` |
| Port | `9090` |
| Route | `testapi.localhost` → port 9090 |
| Service | `testapi` (`uvicorn app:app --host 0.0.0.0 --port 9090`, `restart: always`) |
| Install files | `pixi.toml`, `app.py` |

## How to use it

```yaml
my-image:
  candy:
    - '@github.com/opencharly/pod-testapi:<tag>'
```

## Verification

The candy's `check:` plan asserts the installed `app.py`, that the pixi env can
import `fastapi` and `uvicorn`, and — at deploy scope — that `GET /health` and
`GET /` return HTTP 200 with the expected bodies, plus the running `testapi`
service and a reachable `127.0.0.1:${HOST_PORT:9090}`.

## Layout

- `charly.yml` — the `testapi:` candy entity (description, `require`, `port`,
  `route`, `service`, `plan`) plus its `skill:` entity.
- `app.py` — the FastAPI application module.
- `pixi.toml`, `pixi.lock` — the service's Python environment.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-infrastructure:testapi` — the candy properties and the
  route/service shape.
- `/charly-infrastructure:supervisord` — the process-manager dependency.
- `/charly-infrastructure:traefik` — the reverse proxy for route handling.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
