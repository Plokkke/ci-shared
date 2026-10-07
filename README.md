# ci-shared

Building blocks shared by the projects deployed on the home server.

## Request a deployment

`.github/workflows/deploy.yml` of a deployed repository:

```yaml
name: Deploy
on:
  workflow_dispatch:
    inputs:
      version: { description: "Version to deploy (empty: latest deployed, current infrastructure)", required: false }
  workflow_call:
    inputs:
      version: { type: string, required: false }
  push:
    branches: [main]
    paths: [infrastructure/**]
permissions: { deployments: write, contents: read }
concurrency: deploy-production
jobs:
  request:
    uses: Plokkke/ci-shared/.github/workflows/request-deploy.yml@v1
    with:
      version: ${{ inputs.version }}
```

`release.yml` calls it after publishing the images: `uses: ./.github/workflows/deploy.yml` with `version: ${{ needs.release.outputs.version }}`.
Rollback: `gh workflow run deploy -R <repo> -f version=<previous>`.

## Renovate

`renovate.json` of a repository: `{"extends": ["github>Plokkke/ci-shared//renovate/default"]}`.
