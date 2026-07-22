# helm

A [Dagger](https://dagger.io) module for [Helm](https://helm.sh) chart
checks: lint every chart, and assert that every values file renders.

## Functions

| Function          | Description                                                        |
| ----------------- | ------------------------------------------------------------------ |
| `charts`          | The discovered charts as `Chart` objects.                          |
| `chart`           | One discovered chart by its path.                                  |
| `lint`            | `helm lint` every visible chart, across its values matrix (a `@check`). |
| `assert-template` | Assert every discovered values file renders with `helm template --dry-run=client` (a `@check`). |

## Usage

Install the module in your workspace:

```sh
dagger install github.com/dagger/helm
```

Run the checks:

```sh
dagger check                       # run every check in the workspace
dagger check helm:lint             # just chart linting
dagger check helm:assert-template  # just the values render assertions
```

## Values matrix

Each chart is checked with its default values plus every values file matching
`valuesGlob` — defaulting to `ci/*-values.yaml`, [Helm chart-testing's CI
values convention](https://github.com/helm/chart-testing/blob/main/doc/ct_lint.md).
`assert-template` intentionally skips the bare chart: some charts only render
cleanly with one of their explicit values files.

## Working directory awareness

Chart discovery searches for `Chart.yaml` at or below your current working
directory, so running from a subdirectory checks only the charts in that
subtree; chart paths are reported relative to it.

Each chart is mounted whole — the directory containing `Chart.yaml` is the
chart boundary, so values, templates, dependencies, CRDs, and files
referenced via `.Files` are all available — and each chart/values dimension
runs in its own container, so results cache per dimension.

## Settings

- **`version`** (default `3.18.4`): the Helm version to use.
- **`valuesGlob`** (default `ci/*-values.yaml`): glob for chart values files
  to check as extra matrix dimensions.
