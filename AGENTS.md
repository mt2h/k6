# AGENTS.md

Guidance for AI agents working in this repository.

## What this repo is

A k6 load-testing course. k6 is installed natively and run from the console (`k6 run`). Docker is an optional alternative, not the default. Test scripts live in `scripts/`.

## Layout

- `scripts/` — k6 test scripts written in JavaScript (ES modules).
- `EXAMPLES.md` — per-example docs: how to run each script, what it does, and its output.
- `README.md` — course overview and usage.

## Running tests

Run k6 from the console with a native install:

```bash
k6 run scripts/<script>.js
```

k6 is a single-run task that prints results to the console and exits, not a persistent service. Docker is available as an optional alternative:

```bash
docker run --rm -v "$PWD/scripts:/scripts" grafana/k6 run /scripts/<script>.js
```

## Conventions

- Place every test script under `scripts/`.
- Write scripts as ES modules: `import http from 'k6/http';` with a default exported function.
- Keep the load profile in the `options` export (`vus`, `duration`, `stages`, `thresholds`).
- In console examples, reference scripts by their host path (`scripts/...`); for the Docker alternative, use the in-container path (`/scripts/...`).
- Results are read from the console output; k6 does not expose a port.
- Write all repository content — docs, comments, commit messages — in English.

## Verifying changes

After adding or editing a script, run it to confirm it executes and passes:

```bash
k6 run scripts/<script>.js
```

A non-zero exit code or failed checks/thresholds means the test did not pass — fix it before considering the work done.

## Out of scope

- Do not reintroduce a long-running service setup; k6 runs as a one-shot console command.
