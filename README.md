# roxctl-installer-action GitHub Action

This is a GitHub action for installing `roxctl` on Github Action runners. `roxctl` is a command-line interface (CLI) for running commands on Red Hat Advanced Cluster Security for Kubernetes (RHACS).

### Example

```yaml
      - uses: ivan-degtiarenko/test-repo/actions/install-roxctl@main
        with:
          install-dir: /usr/local/bin
          roxctl-release: 4.4.0

      - name: Validate the roxctl install and its token
        shell: bash
        run: roxctl image scan --image=nginx:latest --endpoint=${{ env.RHACS_ENDPOINT }} --password=${{ secrets.RHACS_PASSWORD }}
```

### Parameters

| Parameter name | Required? | Description |
| --- | --- | --- |
| `install-dir` | (optional) | Path of directory to install `roxctl` to. |
| `version` | (optional) | `roxctl` release version to use, e.g. "4.4.0". The latest available version is used by default. |
