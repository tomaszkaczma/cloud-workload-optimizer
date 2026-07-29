# Contributing

Contributions that improve correctness, reproducibility, documentation, or
benchmark coverage are welcome.

## Development setup

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -e ".[dev]"
```

## Checks

Run these commands before opening a pull request:

```bash
ruff check .
mypy src
python -m unittest discover -s tests -v
python -m build
```

Generated baseline results should only be updated when the simulator, search
space, constraints, or default seed changes. Explain the reason in the pull
request and include the updated `results/summary.json`.

## Scope

Please do not label simulated outputs as real cloud measurements. Provider-
specific benchmarks should document the machine type, region, runtime,
dataset, repetitions, pricing snapshot, and measurement timestamp.
