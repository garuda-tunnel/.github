# garuda-tunnel/.github

Shared reusable GitHub Actions workflows for the `garuda-tunnel` org.

## Workflows

### `publish-component.yml`

Builds and pushes a Docker image to GHCR. Optionally packages and pushes a Helm chart to OCI.
Uses `GITHUB_TOKEN` — no extra secrets required.

**Usage:**
```yaml
jobs:
  publish:
    uses: garuda-tunnel/.github/.github/workflows/publish-component.yml@main
    with:
      image_name: ghcr.io/garuda-tunnel/garuda-example
      image_context: image/
      chart_path: charts/example   # omit for image-only repos
    secrets: inherit
```

| input | required | description |
|---|---|---|
| `image_name` | yes | Full GHCR image name |
| `image_context` | yes | Docker build context path |
| `chart_path` | no | Helm chart dir; empty = no chart publish |
| `chart_registry` | no | OCI target (default: `oci://ghcr.io/garuda-tunnel/charts`) |

### `publish-public.yml`

Builds a filtered snapshot of a private repo and pushes it to a public target repo.
Requires `PUBLISH_PAT` secret (provisioned by `garuda-tunnel/org-provision` Terraform).

**Usage:**
```yaml
jobs:
  publish:
    uses: garuda-tunnel/.github/.github/workflows/publish-public.yml@main
    with:
      public_repo: garuda-tunnel/garuda
      release_message: ${{ inputs.release_message }}
      dry_run: ${{ inputs.dry_run == 'true' }}
    secrets:
      PUBLISH_PAT: ${{ secrets.PUBLISH_PAT }}
```

## Secrets

`PUBLISH_PAT` is provisioned per-repo by Terraform in `garuda-tunnel/org-provision`.
It is a fine-grained PAT scoped to `Contents: Read+Write` on the target public mirror only.
