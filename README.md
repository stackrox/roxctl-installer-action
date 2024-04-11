# roxctl-installer-action GitHub Action

This is a GitHub action for installing `roxctl` on Github Action runners. `roxctl` is a command-line interface (CLI) for running commands on Red Hat Advanced Cluster Security for Kubernetes (RHACS).

### Example

```yaml
      - uses: stackrox/roxctl-installer-action@main
        with:
          install-dir: /usr/local/bin
          roxctl-release: 4.4.0
          central-endpoint: ${{ env.RHACS_ENDPOINT }}
          central-token: ${{ secrets.RHACS_API_TOKEN }}

      - name: Validate the roxctl install and its token
        shell: bash
        run: |
          ROX_API_TOKEN="${{ secrets.RHACS_API_TOKEN }}"
          roxctl image scan --image=nginx:latest --endpoint=${{ env.RHACS_ENDPOINT }}
```

### Parameters

| Parameter name     | Required? | Description                                                                                                                                                          |
|--------------------| --- |----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `install-dir`      | (optional) | Path of directory to install `roxctl` to.                                                                                                                            |
| `version`          | (optional) | `roxctl` release version to use, e.g. "4.4.0". The latest available version is used by default. Ignored when `central-endpoint` is specified.                        |
| `central-endpoint` | (optional) | RHACS Central endpoint to download `roxctl` from. If left unspecified, `roxctl` is downloaded from mirror.openshift.com instead. Requires `central-token` to be set. |
| `central-token`    | (optional) | Token (can be obtained using API token or short-lived token functionality) to access RHACS Central endpoint.                                                         |
