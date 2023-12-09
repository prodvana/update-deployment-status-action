# update-deployment-status Github Action

This action updates the status of a deployment on GitHub Actions to Prodvana. This is meant to be used in conjunction with record-deployment-action, with `pending: true`.


# Requirements
- [pvnctl](https://github.com/prodvana/pvnctl) present in the CI environment
  - We recommend using [init-pvnctl-action](https://github.com/prodvana/init-pvnctl-action) to handle installation and authentication initialization

# Usage

## Inputs

| Input             | Default        | Description                                                                                                                                              |
| ----------------- | -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| deployment_id     | (required)     | The deployment ID returned from record-deployment action                                                                                                       |
| failed            | false          | Mark deployment as failing                                                                                                                                  |
| auth_context      | default        | pvnctl auth context to use. If you're using this action with init-pvnctl, leave as the default                                                           |


## Basic Usage

```yaml
steps:
  # pvnctl must be installed in your Action environment for configs-apply
  - uses: prodvana/init-pvnctl-action@v0.1.0
    with:
      org: my-org
      api_token: ${{ secrets.YOUR_PRODVANA_API_TOKEN }}
  - uses: prodvana/record-deployment-action@v0.1.2
    id: record-deployment
    with:
      app: product-name
      service: web
      release_channel: prod
      pending: true
  # do actual deployment, then add these at the end
  - uses: prodvana/update-deployment-status-action@v0.1.1
    with:
      deployment_id: ${{ steps.record-deployment.outputs.deployment_id }}
  - uses: prodvana/update-deployment-status-action@v0.1.1
    if: failure()
    with:
      deployment_id: ${{ steps.record-release.outputs.deployment_id }}
      failed: true
```
