# k6 Course

A hands-on course for learning [k6](https://grafana.com/docs/k6/latest/), the open-source load testing tool by Grafana. This repo runs k6 through Docker, so you don't need to install it locally.

## Requirements

- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/) (Compose v2, invoked as `docker compose`).

That's the only prerequisite. k6 itself runs inside the official `grafana/k6` container.

## Project structure

```
.
├── docker-compose.yml   # Runs k6 via the official grafana/k6 image
├── scripts/             # k6 test scripts (mounted into the container at /scripts)
│   └── script.js        # Sample load test
├── README.md
└── AGENTS.md
```

## Running tests

Run the default sample script:

```bash
docker compose run --rm k6 run /scripts/script.js
```

Run any other script you add to `scripts/`:

```bash
docker compose run --rm k6 run /scripts/my-test.js
```

Override k6 options from the CLI:

```bash
docker compose run --rm k6 run --vus 10 --duration 30s /scripts/script.js
```

`run --rm` executes k6 once and removes the container afterwards. k6 is a one-shot task, not a long-running service, so there is no `docker compose up`.

## Writing a test

Add a new `.js` file under `scripts/`. A minimal test:

```javascript
import http from 'k6/http';
import { sleep, check } from 'k6';

export const options = {
  vus: 5,
  duration: '10s',
};

export default function () {
  const res = http.get('https://test.k6.io');
  check(res, {
    'status is 200': (r) => r.status === 200,
  });
  sleep(1);
}
```

- `options` defines the load profile (virtual users, duration, stages, thresholds).
- The default exported function is the code each virtual user runs in a loop.
- `check` makes assertions; `sleep` paces each iteration.

## Browser tests

For browser-based tests, use the image with Chromium preinstalled:

```bash
docker run --rm -v "$PWD/scripts:/scripts" grafana/k6:master-with-browser run /scripts/my-browser-test.js
```

## Resources

- [k6 documentation](https://grafana.com/docs/k6/latest/)
- [Installation guide](https://grafana.com/docs/k6/latest/set-up/install-k6/)
- [Examples](https://grafana.com/docs/k6/latest/examples/)
- [JavaScript API reference](https://grafana.com/docs/k6/latest/javascript-api/)
