This releases a Python package to IAM's PyPI on Google Artifact Registry (GAR).

# What it does

* Install (`poetry install --with=dev`) and test (`make ci-test`) the project.
* Check if the package exists on GAR PyPI.
* Build and upload it if it does not.

# How to use it

1. Check if you code can publish e.g., that a new version is defined in pyproject.toml
1. Use this

Something like this...

```yaml
name: Release on push to `main`
on:
  push:
    branches:
      - main
    paths:
      - pyproject.toml
jobs:
  publish-if-new-version:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout with previous commit for pyproject.toml comparison
        uses: actions/checkout@v3
        with:
          # get previous commit so we can compare pyproject.toml below
          fetch-depth: 2

      - name: Check pyproject.toml version to see if a new release is necessary
        run: |
          set -x  # print commands and their arguments as they are executed
          diff=$(git diff HEAD:pyproject.toml HEAD~1:pyproject.toml) || exit 0
          echo "$diff" | grep '+version ='
          if [[ $? -ne 0 ]];
          then
            echo 'version in pyproject.toml did not change - not publishable'
            exit 1
          fi

      - name: Publish new package
        uses: UWIT-IAM/action-release-py-package@v1
        with:
          pypi: "${{ vars.IAM_GAR_PYPI }}"
          credentials: "${{ secrets.MCI_GCLOUD_AUTH_JSON }}"

```

# Expectations

* Python code is `poetry` based
    - `poetry install --with=dev` works
* `make ci-test` works and the return code reflects if the tests passed
