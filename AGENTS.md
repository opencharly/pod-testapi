# AGENTS.md — pod-testapi

Standalone candy repo for the `testapi` candy — a FastAPI test service answering
HTTP on port 9090 under supervisord. The candy lives in `charly.yml` at the repo
root plus its app and pixi environment.

Canonical files:

- `charly.yml` — the `testapi:` candy entity (description, `require`, `port`,
  `route`, `service`, `plan`) and its `skill:` entity.
- `app.py` — the FastAPI application module (`GET /`, `GET /health`).
- `pixi.toml`, `pixi.lock` — the service's Python environment.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:testapi` — the owning skill: the candy properties, the
  route, and the service shape. Load before editing, building, deploying, or
  troubleshooting this candy.
- `/charly-infrastructure:supervisord` — the process-manager dependency.
- `/charly-infrastructure:traefik` — the reverse proxy for route handling.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `copy:` / `check:`, service declarations).
- `/charly-check:check` — the check/R10 framework (`charly check box`,
  `charly check run <bed>`).
- `/charly-pod:pod` — the `kind: pod` / deploy schema reference (this candy is
  composed into a box; ports, routes, services).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The candy's own `check:` steps assert the installed `app.py`, the pixi env's
  `fastapi` / `uvicorn` imports, and — at deploy scope — HTTP 200 on `/health`
  and `/`, the running `testapi` service, and a reachable published port.

## Modify this repo

- Edit the `testapi:` candy entity in `charly.yml`; the `skill:` entity in the
  same file is the owning skill's source — a candy change and its skill change
  land together.
- `app.py` is copied to the service working directory; keep its destination in
  step with the service `working_directory` and the `uvicorn app:app` target.
- The `route:` host `testapi.localhost` and port 9090 are the service contract;
  keep them in step with the `port:` list and the `http:` checks.
- The `skill:` entity is the source for `/charly-infrastructure:testapi`; never
  edit the generated `SKILL.md` — regenerate it.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
