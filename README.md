# k6 Course

A hands-on course for learning [k6](https://grafana.com/docs/k6/latest/), the open-source load testing tool by Grafana. Install k6 locally and run the examples from the console.

## Requirements

- A native k6 install (see below). Docker is an optional alternative.

## Installing k6 on Fedora

Install k6 with `dnf`:

```bash
sudo dnf install https://dl.k6.io/rpm/repo.rpm
sudo dnf install k6
```

Verify the install:

```bash
k6 version
```

## Project structure

```
.
├── scripts/       # k6 test scripts
├── EXAMPLES.md    # Each example: how to run it, what it does, and its output
├── README.md
└── AGENTS.md
```

## Running tests

Run a script from the console:

```bash
k6 run scripts/<script>.js
```

k6 runs as a one-shot process: it executes the test, prints the results to the console, and exits. It is a command-line tool, not a web server — results are read from the terminal.

### Optional: running with Docker

If you prefer not to install k6, you can run it through the official image, mounting `scripts/` into the container at `/scripts`:

```bash
docker run --rm -v "$PWD/scripts:/scripts" grafana/k6 run /scripts/<script>.js
```

## Examples

See [EXAMPLES.md](EXAMPLES.md) for each example script: the command to run it, what it does, and the output it produces.

## Resources

- [k6 documentation](https://grafana.com/docs/k6/latest/)
- [Installation guide](https://grafana.com/docs/k6/latest/set-up/install-k6/)
- [Examples](https://grafana.com/docs/k6/latest/examples/)
- [JavaScript API reference](https://grafana.com/docs/k6/latest/javascript-api/)
