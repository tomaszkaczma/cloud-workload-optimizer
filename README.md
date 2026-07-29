# Poker Hand Classification

[![Tests](https://github.com/tomaszkaczma/Machine-Learning-Coursework/actions/workflows/tests.yml/badge.svg)](https://github.com/tomaszkaczma/Machine-Learning-Coursework/actions/workflows/tests.yml)
[![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-3776AB.svg)](https://www.python.org/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-ML-F7931E.svg)](https://scikit-learn.org/)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

A leakage-safe, reproducible comparison of **Categorical Naïve Bayes** and
**Random Forest** for severely imbalanced ten-class poker-hand
classification—with deterministic poker rules as the production benchmark.

This project modernises an earlier Python/MATLAB coursework submission. The
rewrite replaces archived notebooks, invalid synthetic hands and
training-set evaluation with an installable Python package, semantic data
validation, out-of-sample metrics, automated tests and a corrected MATLAB
reference implementation.

## Results

Models were trained on the official **25,010-row training file** and evaluated
once on the untouched **1,000,000-row test file**.

| Method | Features | Accuracy | Balanced accuracy | Macro F1 |
|---|---|---:|---:|---:|
| Deterministic poker rules | Rule-based | **100.0000%** | **100.00%** | **100.00%** |
| Random Forest | Engineered | **99.9996%** | **96.67%** | **94.66%** |
| Categorical Naïve Bayes | Engineered | 99.9802% | 91.63% | 86.59% |
| Random Forest | Raw | 61.0373% | 13.43% | 13.30% |
| Categorical Naïve Bayes | Raw | 49.3952% | 9.96% | 7.64% |

![Model comparison](assets/model_comparison.png)

The engineered Random Forest misclassified four of one million test hands.
Its macro F1 is lower than its accuracy because the rarest classes have very
small support: the official test file contains only 12 Straight Flushes and
three Royal Flushes.

The perfect rule result is expected. Poker classes are deterministic functions
of rank and suit, so rules are the correct production solution. The
machine-learning comparison remains useful as a demonstration of:

- severe multiclass imbalance;
- categorical modelling;
- leakage prevention;
- domain-aware feature engineering;
- the difference between accuracy and class-balanced metrics.

Exact machine-readable results and software versions are available in
[`assets/benchmark_metrics.json`](assets/benchmark_metrics.json).

## Why the original workflow was replaced

The historical notebooks applied ordinary SMOTE directly to suit/rank columns.
The saved synthetic dataset was audited against poker rules:

| Finding | Count | Rate |
|---|---:|---:|
| Duplicate feature rows | 91,560 | 73.29% |
| Hands containing the same card twice | 53,591 | 42.90% |
| Labels inconsistent with the cards | 75,899 | 60.75% |
| Changed/generated rows with incorrect labels | 75,899 of 98,048 | 77.41% |

The original training and test files had zero invalid hands and zero semantic
label mismatches.

The historical MATLAB code then cross-validated the corrupted export and used
`resubPredict` for confusion matrices. This measured training performance, not
performance on unseen official data. The full investigation is documented in
[`docs/ORIGINAL_CODE_AUDIT.md`](docs/ORIGINAL_CODE_AUDIT.md).

## Methodology

### Data validation

Every input file is checked for:

- exactly eleven integer-coded columns;
- suits between 1 and 4;
- ranks between 1 and 13;
- targets between 0 and 9;
- five distinct cards in every hand;
- target labels consistent with deterministic poker rules.

A corrupted generated dataset therefore fails before training.

### Feature representations

The `raw` experiment uses the ten UCI predictors in their supplied order.

The `engineered` experiment:

1. sorts cards by rank and suit so equivalent permutations share one
   representation;
2. counts unique ranks and suits;
3. calculates maximum rank and suit frequencies;
4. identifies pairs, triplets and four-of-a-kind patterns;
5. adds straight and flush indicators.

All features are calculated from predictor cards only.

![Random Forest feature importance](assets/random_forest_feature_importance.png)

### Evaluation

The project exports:

- accuracy;
- balanced accuracy;
- macro and weighted F1;
- precision, recall, F1 and support for every class;
- raw and row-normalised confusion matrices;
- Random Forest feature importance;
- fit and prediction timings.

See [`docs/METHODOLOGY.md`](docs/METHODOLOGY.md) for the full experimental
contract.

## Quick start

Replacing the historical repository? Follow
[`UPLOAD_INSTRUCTIONS.md`](UPLOAD_INSTRUCTIONS.md).

```bash
git clone https://github.com/tomaszkaczma/Machine-Learning-Coursework.git
cd Machine-Learning-Coursework

python -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -e .

python -m poker_hand_ml
```

Windows PowerShell activation:

```powershell
.venv\Scripts\Activate.ps1
```

The experiment command downloads the official UCI archive into `data/raw/`,
validates both files, runs five benchmarks and writes results to `artifacts/`.

Equivalent console command:

```bash
poker-hand-ml --output-dir artifacts --n-estimators 300
```

Use existing files:

```bash
poker-hand-ml \
  --train path/to/poker-hand-training-true.data \
  --test path/to/poker-hand-testing.data \
  --output-dir artifacts
```

## Audit a dataset

The standalone audit command is useful before accepting any generated or
third-party file:

```bash
poker-hand-audit \
  data/raw/poker-hand-training-true.data \
  data/raw/poker-hand-testing.data
```

JSON output:

```bash
poker-hand-audit data/raw/poker-hand-training-true.data --json
```

## Notebook

Install the optional notebook dependency and open the reproducible walkthrough:

```bash
python -m pip install -e ".[notebook]"
jupyter lab notebooks/01_model_comparison.ipynb
```

The notebook contains no duplicated training logic; it calls the tested package
functions.

## MATLAB reference

The corrected MATLAB implementation is in [`matlab/`](matlab/README.md). It:

- validates cards and labels;
- fits an actual bagged Random Forest;
- treats Naïve Bayes predictors as categorical;
- uses no SMOTE or logarithmic transformation;
- predicts only against the official test set;
- exports class-balanced metrics and confusion matrices.

Python is the primary continuously tested implementation. MATLAB requires
Statistics and Machine Learning Toolbox.

## Generated outputs

Each Python run creates:

- `results.json`;
- `model_comparison.csv` and `.png`;
- one class-metrics CSV per experiment;
- one normalised confusion matrix per experiment;
- feature-importance CSV and chart;
- optional serialised pipelines with `--save-models`.

![Engineered Random Forest confusion matrix](assets/random_forest_engineered_confusion_matrix.png)

## Repository structure

```text
.
├── assets/                     # Reproduced benchmark figures and metrics
├── data/raw/                   # Downloaded UCI data; not committed
├── docs/                       # Methodology and original-code audit
├── matlab/                     # Corrected MATLAB reference
├── notebooks/                  # Thin reproducible walkthrough
├── src/poker_hand_ml/
│   ├── audit.py                # Structural and semantic data audit
│   ├── data.py                 # Download, loading and schema validation
│   ├── evaluation.py           # Metrics and visualisation
│   ├── experiment.py           # Command-line experiment runner
│   ├── features.py             # Order-invariant features
│   ├── models.py               # Reproducible model factories
│   └── rules.py                # Poker rules and semantic validation
├── tests/                      # Automated Python tests
├── .github/                    # CI workflow and issue template
├── CITATION.cff
├── CONTRIBUTING.md
├── LICENSE
└── pyproject.toml
```

## Tests

```bash
python -m pip install -e ".[dev]"
ruff check .
pytest --cov=poker_hand_ml --cov-report=term-missing
```

GitHub Actions runs linting and tests on every push and pull request.

## Limitations

- Minority-class estimates remain uncertain because some official test classes
  contain very few examples.
- Impurity-based Random Forest importance is not causal.
- Engineered features closely encode known poker definitions; this is expected
  and explicitly disclosed.
- Timings vary by hardware and software version.
- The MATLAB reference is provided for continuity with the coursework, while
  Python is the verified implementation.

## Citation and licence

Dataset:

> Cattral, R. and Oppacher, F. (2002). *Poker Hand* [Dataset]. UCI Machine
> Learning Repository. [https://doi.org/10.24432/C5KW38](https://doi.org/10.24432/C5KW38).

The dataset is distributed under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Project source code
is available under the [MIT License](LICENSE).
