[![StepSecurity Maintained Action](https://raw.githubusercontent.com/step-security/maintained-actions-assets/main/assets/maintained-action-banner.png)](https://docs.stepsecurity.io/actions/stepsecurity-maintained-actions)

# Log in to a container registry
Use this GitHub Action to [log in to a private container registry](https://docs.docker.com/engine/reference/commandline/login/) such as [Azure Container registry](https://azure.microsoft.com/en-us/services/container-registry/). Once login is done, the next set of actions in the workflow can perform tasks such as building, tagging and pushing containers.

```yaml
- uses: step-security/azure-docker-login@v2
  with:
    login-server: '<login server>' # default: index.docker.io
    username: '<username>'
    password: '<password>'
```
Refer to the action metadata file for details about all the inputs: [action.yml](https://github.com/step-security/azure-docker-login/blob/main/action.yml)

When using the docker-login action, ensure your login-server matches the fully qualified path to your image. You should omit login-server if you are pushing to the default docker hub without a host prefix, for instance, `docker push repo/image`. You'll need to specify a login-server if you are using the fully qualified path: `docker push index.docker.io/repo/image`. 

## Logging in to multiple registries
To log in to multiple registries, simply run this action several times with different credentials; they will accumulate.

```yaml
- uses: step-security/azure-docker-login@v2
  with:
    login-server: contoso.azurecr.io
    username: ${{ secrets.ACR_USERNAME }}
    password: ${{ secrets.ACR_PASSWORD }}
- uses: step-security/azure-docker-login@v2
  with:
    login-server: index.docker.io
    username: ${{ secrets.DOCKERIO_USERNAME }}
    password: ${{ secrets.DOCKERIO_PASSWORD }}
- run: |
    docker pull contoso.azurecr.io/private/image:latest
    docker pull private/image:latest
```

## You can build and push container registry by using the following example
```yaml
- uses: step-security/azure-docker-login@v2
  with:
    login-server: contoso.azurecr.io
    username: ${{ secrets.REGISTRY_USERNAME }}
    password: ${{ secrets.REGISTRY_PASSWORD }}

- run: |
    docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
    docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
```

### Prerequisite
Get the username and password of your container registry and create secrets for them. For Azure Container registry refer to **admin [account document](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-authentication#admin-account)** for username and password.

Now add the username and password as [a secret](https://developer.github.com/actions/managing-workflows/storing-secrets/) in the GitHub repository.
