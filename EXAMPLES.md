# Examples

Each example in `scripts/` is documented here: the command to run it, what it does, and the output it produces.

Examples are run from the console with a native k6 install (see the README for Fedora install steps):

```bash
k6 run scripts/<script>.js
```

---

## 01_first-script.js

**Run:**

```bash
k6 run scripts/01_first-script.js
```

**What it does:** Runs 10 virtual users (VUs) that repeatedly send a `GET` request to `https://test.k6.io` for 10 seconds, pausing 1 second between iterations. This is the simplest possible load test — a single request in a loop — used to introduce the k6 execution model and summary output.

**Output:**

```
     execution: local
        script: scripts/01_first-script.js
        output: -

     scenarios: (100.00%) 1 scenario, 10 max VUs, 40s max duration (incl. graceful stop):
              * default: 10 looping VUs for 10s (gracefulStop: 30s)

running (10.3s), 00/10 VUs, 80 complete and 0 interrupted iterations
default ✓ [ 100% ] 10 VUs  10s

  █ TOTAL RESULTS

    HTTP
    http_req_duration..............: avg=91.87ms min=11.9ms med=110.1ms max=182.65ms p(90)=165.44ms p(95)=182.03ms
    http_req_failed................: 0.00%  0 out of 160
    http_reqs......................: 160    15.57/s

    EXECUTION
    iteration_duration.............: avg=1.28s
    iterations.....................: 80     7.78/s
    vus............................: 10     min=10 max=10
    vus_max........................: 10     min=10 max=10

    NETWORK
    data_received..................: 386 kB 38 kB/s
    data_sent......................: 42 kB  4.1 kB/s
```

**How to read it:**
- **scenarios** — the load profile k6 ran: 10 VUs looping for 10 seconds.
- **http_reqs** — total HTTP requests sent and the rate per second.
- **http_req_failed** — share of failed requests (`0.00%` means every request succeeded).
- **http_req_duration** — response time stats; `p(90)`/`p(95)` are the 90th/95th percentiles.
- **iterations** — how many times the default function ran across all VUs.
- **data_received / data_sent** — network throughput during the test.

For a deeper explanation of these metrics — percentiles, RPS, iteration vs. request duration, thresholds, and scalability testing — see [KNOWLEDGE.md](KNOWLEDGE.md).
