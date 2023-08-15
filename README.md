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
      - name: Publish if new package
        uses: 'uwit-iam/action-release-py-package@main'
        with:
          pypi: "${{ vars.IAM_GAR_PYPI }}"
          credentials: "${{ secrets.MCI_GCLOUD_AUTH_JSON }}"
```

# Expectations

* Python code is `poetry` based
    - `poetry install --with=dev` works
* `make ci-test` works and the return code reflects if the tests passed
