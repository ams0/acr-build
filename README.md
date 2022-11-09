# Build images with Azure Container Registry

This action can be used to build containers using an Azure Container Registry. 

## Action capabilities

Following the capabilities of this action:

- **Custom Dockerfile path:** It is possible to specify a path for the `Dockerfile`

- **Build context:** Allows to specify the build context for the image

- **Default tag:** The action will default to use the first 8 characters of the commit SHA if no tag is specified

- **Private repositories:**: It is possible to use private Github repositories by providing a `github_token`


## Action inputs:

<table>
  <thead>
    <tr>
      <th>Action inputs</th>
      <th>Description</th>
      <th>Default</th>
    </tr>
  </thead>

  <tr>
    <td><code>dockerfile</code><br/></td>
    <td>(Optional) Path to the Dockerfile relative to `folder`</td>
    <td><code>./Dockerfile</code></td>
  </tr>

  <tr>
    <td><code>folder</code><br/></td>
    <td>(Optional) Build context for Docker agent</td>
    <td><code>./</code></td>
  </tr>
  
  <tr>
    <td><code>branch</code><br/></td>
    <td>(Optional) Branch to root the context off of</td>
    <td><code>master</code></td>
  </tr>

  <tr>
    <td><code>repository</code><br/></td>
    <td>(Mandatory) The repository on the Azure Container Registry</td>
    <td></td>
  </tr>

  <tr>
    <td><code>image</code><br/></td>
    <td>(Mandatory) Docker image name</td>
    <td></td>
  </tr>

  <tr>
    <td><code>tag</code><br/></td>
    <td>(Optional) Docker image tag</td>
    <td><code>GITHUB_SHA::8</code></td>
  </tr>

  <tr>
    <td><code>git_access_token</code><br/></td>
    <td>(Optional) The Github access token for private repositories</td>
    <td><code>GITHUB_SHA::8</code></td>
  </tr>

  <tr>
    <td><code>registry</code><br/></td>
    <td>(Mandatory)The Azure Container Registry name</td>
    <td></td>
  </tr>
  
  <tr>
    <td><code>build_args</code><br/></td>
    <td>(Optional) Build arguments </td>
    <td></td>
  </tr>

</table>

## Example usage

The action expects the use of the `azure/login` action prior to calling this action, to login into Azure. The login can be done either with Service Principal or [OIDC authentication](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure).

In your repository, create the following secrets (or set them in clear in the workflow definition):

- registry
- repository
- (optional, for accessing private repositories) git_access_token 

In `.github/workflows` create a workflow file like the following:

```yaml
name: build_image
on:
  push:
    paths:
      - "src/docker/**"
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: 'Az CLI login'
      uses: azure/login@v1
      with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}


      - name: ACR build
        id: acr
        uses: azure/acr-build@v1
        with:
          registry: ${{ secrets.registry }}
          repository: ${{ secrets.repository }}
          image: image
          git_access_token: ${{ secrets.git_access_token }}
          folder: src/docker
          dockerfile: ../../dockerfiles/Dockerfile
          branch: main
          build_args: '[{"arg_one":"value_one"}, {"arg_two":"value_two"}]'
```

# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
