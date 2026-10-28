# AGENTS.md

Guidance for AI agents working in this repository.

## What this repo is

A k6 load-testing course. k6 runs through Docker (the official `grafana/k6` image) so no local k6 install is needed. Test scripts live in `scripts/` and are mounted into the container at `/scripts`.

## Layout

- `docker-compose.yml` — defines the `k6` service using `grafana/k6:latest`, mounting `./scripts` to `/scripts`.
- `scripts/` — k6 test scripts written in JavaScript (ES modules).
- `README.md` — course overview and usage.

## Running tests

Always run k6 through Docker Compose, never assume a local `k6` binary:

```bash
docker compose run --rm k6 run /scripts/<script>.js
```

Use `run --rm` (one-shot), not `docker compose up`. k6 is a single-run task, not a persistent service.

## Conventions

- Place every test script under `scripts/` so it is visible inside the container at `/scripts`.
- Write scripts as ES modules: `import http from 'k6/http';` with a default exported function.
- Keep the load profile in the `options` export (`vus`, `duration`, `stages`, `thresholds`).
- Reference scripts by their in-container path (`/scripts/...`) in commands and docs, not the host path.
- Write all repository content — docs, comments, commit messages — in English.

## Verifying changes

After adding or editing a script, run it through Docker to confirm it executes and the checks pass:

```bash
docker compose run --rm k6 run /scripts/<script>.js
```

A non-zero exit code or failed checks/thresholds means the test did not pass — fix it before considering the work done.

## Out of scope

- Do not add instructions to install k6 locally; the course is Docker-based by design.
- Do not convert the `k6` service into a long-running service in `docker-compose.yml`.
