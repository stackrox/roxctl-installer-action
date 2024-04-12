# roxctl-installer-action GitHub Action

This is a GitHub action for installing `roxctl` on Github Action runners. `roxctl` is a command-line interface (CLI) for running commands on Red Hat Advanced Cluster Security for Kubernetes ([RHACS](redhat.com/en/technologies/cloud-computing/openshift/advanced-cluster-security-kubernetes)).

### Example

This example uses [central-login](https://github.com/stackrox/central-login) Github Action to login to ACS Central for `roxctl` download.

```yaml
jobs:
  example:
    runs-on: macos-latest
    permissions:
      id-token: write
    steps:
      - name: Central Login
        uses: stackrox/central-login@v1
        with:
          endpoint: https://${{ env.ROX_ENDPOINT }}
      - uses: stackrox/roxctl-installer-action@main
        with:
          install-dir: /usr/local/bin
          roxctl-release: 4.4.0
          central-endpoint: https://${{ env.ROX_ENDPOINT }}
          central-token: ${{ env.ROX_API_TOKEN }}

      - name: Validate the roxctl install and its token
        shell: bash
        run: |
          roxctl image scan --image=nginx:latest
```

### Parameters

| Parameter name     | Required? | Description                                                                                                                                                         |
|--------------------| --- |---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `install-dir`      | (optional) | Path of directory to install `roxctl` to.                                                                                                                           |
| `version`          | (optional) | `roxctl` release version to use, e.g. "4.4.0". The latest available version is used by default. Ignored when `central-endpoint` is specified.                       |
| `central-endpoint` | (optional) | RHACS Central endpoint to download `roxctl` from. If left unspecified, `roxctl` is downloaded from mirror.openshift.com instead. Requires `central-token` to be set. |
| `central-token`    | (optional) | Token to access RHACS Central endpoint.                                                         |
