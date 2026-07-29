# Contributing

Contributions that improve correctness, reproducibility or explanation are
welcome.

## Local setup

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -e ".[dev]"
```

On Windows PowerShell, activate the environment with:

```powershell
.venv\Scripts\Activate.ps1
```

## Before opening a pull request

```bash
ruff check .
pytest --cov=poker_hand_ml --cov-report=term-missing
```

Please also confirm that:

- the official test data is never used for fitting or model selection;
- resampling, if investigated, occurs only inside training folds;
- generated hands contain five distinct valid cards;
- every generated label agrees with deterministic poker rules;
- benchmark changes include the random seed and software versions;
- raw UCI data and serialised models are not committed.

## Scope

Good contributions include confidence intervals, calibration, explainability,
an interactive demonstration and comparisons with additional leakage-safe
models. Changes that recreate standard SMOTE over suit/rank columns will not be
accepted because interpolation does not preserve valid poker-hand semantics.

