# GitHub upload instructions

## Recommended repository name

`poker-hand-classification`

Suggested description:

> Leakage-safe comparison of Categorical Naive Bayes and Random Forest on the
> UCI Poker Hand dataset, with semantic data validation and deterministic
> rules.

Suggested topics:

`machine-learning`, `python`, `scikit-learn`, `random-forest`, `naive-bayes`,
`multiclass-classification`, `imbalanced-data`, `matlab`, `data-validation`

## Replace the current repository

1. Download and extract the final ZIP.
2. Back up the existing repository if you want to retain its history.
3. Remove the old `Code.zip` and generated CSV files from the GitHub
   repository.
4. Optionally move the original coursework poster into `docs/archive/`.
5. Upload the **contents** of the extracted folder to the repository root.
6. Do not upload `.venv/`, `data/raw/`, `artifacts/`, caches or model files.
7. Commit the changes and wait for the GitHub Actions test workflow.

## Git command workflow

```bash
git clone https://github.com/tomaszkaczma/Machine-Learning-Coursework.git
cd Machine-Learning-Coursework

# Copy the extracted replacement files into this directory.

git add --all
git status
git commit -m "Rebuild poker-hand classification project"
git push origin main
```

Review `git status` before committing. It should show the new package, tests,
documentation, assets and workflows, while the historical nested archive and
generated datasets should be deleted.

## After uploading

1. Confirm the README images render.
2. Open the **Actions** tab and confirm the test workflow passes.
3. Add the suggested repository description and topics.
4. Rename the repository if desired under **Settings → General → Repository
   name**. GitHub normally redirects the old URL.
5. Create a `v1.0.0` release using the changelog as the release notes.
6. Pin the repository on your GitHub profile.

