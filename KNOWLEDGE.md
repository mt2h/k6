# Knowledge: reading k6 results

Reference for interpreting the output of a k6 load test. The [examples](EXAMPLES.md) link here for the metric details.

k6 is a load-testing tool that measures the performance of applications and APIs. After a run it prints a summary grouped into HTTP, EXECUTION, and NETWORK metrics.

## Test configuration

```
scenarios: 1 scenario, 10 max VUs
default: 10 looping VUs for 10s
```

| Parameter | Meaning |
|---|---|
| `10 VUs` | 10 concurrent virtual users |
| `10s` | Duration of the load |
| `30s gracefulStop` | Extra time allowed for in-flight iterations to finish |
| `iterations` | Number of complete script executions |
| `http_reqs` | Number of HTTP requests executed |

**Important:** `10 VUs` does not mean 10 simultaneous requests at all times. Each virtual user runs the script and starts over when its iteration finishes.

## HTTP response times

The `http_req_duration` line reports the distribution of response times:

- **avg** — average response time.
- **med** — median: half of the requests responded in this time or less.
- **p(90)** — 90th percentile: 90% of requests responded in this time or less.
- **p(95)** — 95th percentile: 95% of requests responded in this time or less.
- **max** — the slowest request.

The **p95** is especially useful for evaluating performance: it captures the experience of the slower requests without relying solely on the maximum.

`http_req_failed` is the share of failed requests. `0.00%` means every request met k6's expected-response criteria — but that alone does not guarantee the response *data* was correct, nor that the app tolerates higher load. Use `check()` assertions and larger tests to confirm.

## Requests and throughput

```
http_reqs........: 180    16.14/s
iterations.......: 90      8.07/s
```

- The app served roughly **16.14 requests per second (RPS)** over the measured window.
- About **8.07 iterations per second** completed.
- Each iteration made ~2 HTTP requests on average (`http_reqs / iterations`).

Note the gap between iteration and request duration:

```
iteration_duration: avg=1.23s
http_req_duration:  avg=86.27ms
```

An iteration takes ~1.23s even though a single HTTP request takes ~86ms. This is usually because the script calls `sleep(1)`, makes several requests, or does extra work — it does **not** mean the API itself took 1.23s to respond.

## Did the application pass?

```
http_req_failed: 0.00%  0 out of 180
```

All requests were considered successful by k6. However, a 0% error rate does not prove the app scales further or returns correct data. To verify that, add validations and test with more users.

## Testing scalability

Use a staged (ramping) profile to see how the app behaves as concurrency grows, and define `thresholds` as pass/fail objectives:

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 10 },
    { duration: '30s', target: 50 },
    { duration: '30s', target: 100 },
    { duration: '30s', target: 200 },
    { duration: '30s', target: 0 },
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'],
    http_req_failed: ['rate<0.01'],
  },
};

export default function () {
  const res = http.get('https://your-api.com/health');

  check(res, {
    'status 200': (r) => r.status === 200,
  });

  sleep(1);
}
```

This ramps virtual users progressively and evaluates two objectives:

- **p95** stays below 500 ms.
- HTTP error rate stays below 1%.

Run scalability tests only in an authorized environment with enough capacity — especially against production.

## Going further

Beyond raw numbers, a load test can answer:

- How many RPS the application actually sustains.
- When latency starts to degrade.
- How autoscaling (HPA, KEDA, Karpenter) reacts under the load, for services running in Kubernetes.

The right next step depends on the stack under test (Go, Python, Node.js, or a Kubernetes-hosted service).
