# AGENTS.md

Guidance for AI agents working in this repository.

## What this repo is

A k6 load-testing course. k6 runs through Docker (the official `grafana/k6` image) so no local k6 install is needed. Test scripts live in `scripts/` and are mounted into the container at `/scripts`.

## Layout

- `scripts/` — k6 test scripts written in JavaScript (ES modules).
- `EXAMPLES.md` — per-example docs: how to run each script, what it does, and its output.
- `README.md` — course overview and usage.

## Running tests

Always run k6 through Docker with `docker run`, never assume a local `k6` binary. Mount `scripts/` into the container at `/scripts`:

```bash
docker run --rm -v "$PWD/scripts:/scripts" grafana/k6 run /scripts/<script>.js
```

Use `--rm` (one-shot). k6 is a single-run task that prints results to the console and exits, not a persistent service.

## Conventions

- Place every test script under `scripts/` so it is visible inside the container at `/scripts`.
- Write scripts as ES modules: `import http from 'k6/http';` with a default exported function.
- Keep the load profile in the `options` export (`vus`, `duration`, `stages`, `thresholds`).
- Reference scripts by their in-container path (`/scripts/...`) in commands and docs, not the host path.
- Results are read from the console output; k6 does not expose a port.
- Write all repository content — docs, comments, commit messages — in English.

## Verifying changes

After adding or editing a script, run it through Docker to confirm it executes and passes:

```bash
docker run --rm -v "$PWD/scripts:/scripts" grafana/k6 run /scripts/<script>.js
```

A non-zero exit code or failed checks/thresholds means the test did not pass — fix it before considering the work done.

## Out of scope

- Do not add instructions to install k6 locally; the course is Docker-based by design.
- Do not reintroduce a long-running service setup; k6 runs as a one-shot console command.
