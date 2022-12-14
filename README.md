# Update Full-Stack Preview Environment to K8s

Uses Open Source Uffizzi CLI `uffizzi preview update` to update an on-demand, ephemeral test environment onto your Kubernetes cluster.

## Inputs

### `server`

URL of your Uffizzi installation (default: https://app.uffizzi.com)

### `username`

Uffizzi username

### `password`

Your Uffizzi password. Specify a GitHub Encrypted Secret and use it! See example below.

### `project`

Uffizzi project slug

### `request-token`

Access token obtained from the GHA pipeline available as `ACTIONS_RUNTIME_TOKEN` in the pipeline environment. See example below.

### `request-token_url`

URL where the pipeline OIDC token can be requested from available as `ACTIONS_ID_TOKEN_REQUEST_URL` in the pipeline environment. See example below.

### `compose-file`

Path to a compose file within your repository (default: docker-compose.yaml)

### `preview-id`

**Required** ID of Uffizzi existing preview

### `ghcr-username` and `ghcr-access-token`

Your GitHub username and the value of a [Github personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) with access to the `read:packages` scope.

This option is provided as a convenience to get started quickly. For sensitive repositories, we recommend instead connecting your Uffizzi account to GHCR via the web interface or by executing `uffizzi connect ghcr` from a trusted environment.

### `dockerhub-username` and `dockerhub-password`

Your DockerHub username and password.

### `acr-registry-url`, `acr-username`, and `acr-password`

Your Azure Container Registry url, username and password.

### `aws-registry-url`, `aws-access-key-id`, and `aws-secret-access-key`

Your Amazon Web Services registry url, access key id and secret access key.

### `gcloud-service-key`

Your Google Cloud service key.

### `docker-registry-url`, `docker-registry-username`, and `docker-registry-password`

Your custom docker registry url, username and password.

## Example usage
### Email and password login

```yaml
uses: UffizziCloud/update-preview-action@v1
with:
  server: 'https://app.uffizzi.com'
  username: 'admin@uffizzi.com'
  password: ${{ secrets.UFFIZZI_PASSWORD }}
  project: 'default'
  compose-file: 'docker-compose.uffizzi.yaml'
  preview-id: 1
```

### OIDC token login

```yaml
update-preview:
  name: "Update Preview on Uffizzi"
  runs-on: ubuntu-20.04
  steps:
    - uses: actions/github-script@v6
      id: ci-job-token
      with:
        script: |
          const token = process.env['ACTIONS_RUNTIME_TOKEN']
          const runtimeUrl = process.env['ACTIONS_ID_TOKEN_REQUEST_URL']
          core.setOutput('request-token', token.trim())
          core.setOutput('request-token-url', runtimeUrl.trim())
    - uses: UffizziCloud/update-preview-action@v1
      with:
        server: 'https://app.uffizzi.com'
        compose-file: 'docker-compose.uffizzi.yaml'
        request-token: ${{ steps.ci-job-token.outputs.request-token }}
        request-token-url: ${{ steps.ci-job-token.outputs.request-token-url }}
        preview-id: 1
permissions:
  id-token: write
```

