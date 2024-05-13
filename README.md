# roxctl-installer-action GitHub Action

This is a GitHub action for installing `roxctl` on Github Action runners. `roxctl` is a command-line interface (CLI) for running commands on Red Hat Advanced Cluster Security for Kubernetes ([RHACS](https://redhat.com/en/technologies/cloud-computing/openshift/advanced-cluster-security-kubernetes)).

![](./docs/images/roxctl-action.png)

## Table of Contents

- [Usage](#usage)
  - [Scan images in CI pipeline](#scan-images-in-ci-pipelines)
  - [Check images in CI pipeline](#check-images-in-ci-pipelines)
  - [Download roxctl from mirror.openshift.com](#download-roxctl-from-mirror-openshift-com)
  - [GitHub code scanning](#github-code-scanning)
  - [Authenticate with static API tokens](#authenticate-with-static-api-tokens)
- [Parameters](#parameters)

## Usage

Short-lived access tokens are the recommended authentication method when using `roxctl` in GitHub workflows.
To generate a suitable token, configure a machine access configuration in Central and run the
[central-login](https://github.com/stackrox/central-login) Github Action to set up an authenticated environment.

The following examples assume `env.CENTRAL_ENDPOINT` to be a valid Central URL.

### Scan images in CI pipelines

```yaml
name: Scan image with roxctl
on:
  push:
    branches: ["main"]
  pull_request:
jobs:
  scan:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - name: Central login
        uses: stackrox/central-login@v1
        with:
          endpoint: ${{ env.CENTRAL_ENDPOINT }}
      - name: Install roxctl
        uses: stackrox/roxctl-installer-action@v1
        with:
          central-endpoint: ${{ env.CENTRAL_ENDPOINT }}
          central-token: ${{ env.ROX_API_TOKEN }}
      - name: Scan image with roxctl
        shell: bash
        run: |
          roxctl image scan --output=table --image="quay.io/stackrox-io/main"
```

### Check images in CI pipelines

```yaml
name: Check image with roxctl
on:
  push:
    branches: ["main"]
  pull_request:
jobs:
  check:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - name: Central login
        uses: stackrox/central-login@v1
        with:
          endpoint: ${{ env.CENTRAL_ENDPOINT }}
      - name: Install roxctl
        uses: stackrox/roxctl-installer-action@v1
        with:
          central-endpoint: ${{ env.CENTRAL_ENDPOINT }}
          central-token: ${{ env.ROX_API_TOKEN }}
      - name: Check image with roxctl
        shell: bash
        run: |
          roxctl image check --output=table --image="quay.io/stackrox-io/main"
```

### Download roxctl from mirror.openshift.com

```yaml
name: Scan image with roxctl
on:
  push:
    branches: ["main"]
  pull_request:
jobs:
  scan:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - name: Central login
        uses: stackrox/central-login@v1
        with:
          endpoint: ${{ env.CENTRAL_ENDPOINT }}
      - name: Install roxctl
        uses: stackrox/roxctl-installer-action@v1
        with:
          version: 4.4.0
      - name: Scan image with roxctl
        shell: bash
        run: |
          roxctl image scan --output=table --image="quay.io/stackrox-io/main"
```

### GitHub code scanning

```yaml
name: Code scanning with roxctl
on:
  push:
    branches: ["main"]
  pull_request:
jobs:
  scan:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      security-events: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Central login
        uses: stackrox/central-login@v1
        with:
          endpoint: ${{ env.CENTRAL_ENDPOINT }}
      - name: Install roxctl
        uses: stackrox/roxctl-installer-action@v1
        with:
          central-endpoint: ${{ env.CENTRAL_ENDPOINT }}
          central-token: ${{ env.ROX_API_TOKEN }}
      - name: Scan image with roxctl
        shell: bash
        run: |
          roxctl image scan --output=sarif --image="quay.io/stackrox-io/main" > results.sarif
      - name: Upload roxctl scan results to GitHub code scanning
        uses: github/codeql-action/upload-sarif@v3
        with:
          category: stackrox-io/main
          sarif_file: results.sarif
```

### Authenticate with static API tokens

Create a repository secret `secrets.ROX_API_TOKEN` and assign its value to a valid Central API token.

```yaml
name: Scan image with roxctl
on:
  push:
    branches: ["main"]
  pull_request:
jobs:
  scan:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - name: Install roxctl
        uses: stackrox/roxctl-installer-action@v1
        with:
          central-endpoint: ${{ env.CENTRAL_ENDPOINT }}
          central-token: ${{ secrets.ROX_API_TOKEN }}
      - name: Scan image with roxctl
        shell: bash
        env:
          ROX_ENDPOINT: ${{ env.CENTRAL_ENDPOINT }}
          ROX_API_TOKEN: ${{ secrets.ROX_API_TOKEN }}
        run: |
          roxctl image scan --output=table --image="quay.io/stackrox-io/main"
```

## Parameters

| Parameter name     | Required?  | Description                                                                                                                                                          |
| ------------------ | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `install-dir`      | (optional) | Path of directory to install `roxctl` to.                                                                                                                            |
| `version`          | (optional) | `roxctl` release version to use, e.g. "4.4.0". The latest available version is used by default. Ignored when `central-endpoint` is specified.                        |
| `central-endpoint` | (optional) | RHACS Central endpoint to download `roxctl` from. If left unspecified, `roxctl` is downloaded from mirror.openshift.com instead. Requires `central-token` to be set. |
| `central-token`    | (optional) | Token to access RHACS Central endpoint.                                                                                                                              |
| `skip-tls-verify`  | (optional) | Skip TLS certificate verification for Central's API endpoint. `false` by default.                                                                                    |
