# Remote hooks guide

Git allows the execution of remote hooks, but GitHub does not provide that feature. 
It has to be done via GitHub Actions.

To set up `flake8` to run when we push into main, we have to define two YAML files.

### 1. Configure Pre-Commit Hooks

Write the following code into the `.pre-commit-config.yaml` file. 
It can be the same as the local hooks. Ensuring a standard across the developers and the server.

```bash
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.3.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml

  - repo: https://github.com/pycqa/flake8
    rev: 6.0.0
    hooks:
      - id: flake8
```

### 2. Set up GitHub Actions

Create a GitHub Actions workflow as `.github/workflows/pre-commit.yml`. 

```bash
name: Pre-Commit Checks

# Define the trigger event
on:
  push:
    branches: [main]
# List the jobs
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'

      - name: Install pre-commit
        run: |
          pip install pre-commit
          pre-commit install-hooks

      - name: Run pre-commit hooks
        run: pre-commit run --all-files
```

### 3. Set up the rules

- On your Github page, go to Settings and on the left bar, select Rules-> Rulesets.
- Select New Ruleset -> New branch ruleset
- Name it and set `Enforcement status` to Active.
- In target branches, select the one you want to protect. In this case, `main`.
- In Branch rules, select `Require status checks to pass`, and link it to the Action you created.

### 4. Run locally

Run the following command to set up the hooks:
```bash
pre-commit install
```

```bash
pre-commit run --all-files
```

