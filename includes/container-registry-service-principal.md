## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para criar uma entidade de serviço com acesso ao registro de contêiner, use o script a seguir. Atualize a variável `ACR_NAME` com o nome do registro de contêiner e, opcionalmente, o valor `--role` no comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para conceder permissões diferentes. Você precisa ter a [CLI do Azure](/cli/azure/install-azure-cli) instalada para usar esse script.

Depois de executar o script, anote a **ID** e a **senha** da entidade de serviço. Quando você tiver suas credenciais, poderá configurar os aplicativos e serviços para se autenticarem no registro de contêiner como a entidade de serviço.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_NAME can be any unique name within your
# subscription (you can use the default below).
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate some values required for subsequent command args
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create the service principal with rights scoped to the registry.
# Default permissions are for both docker push and pull access. Modify the
# '--role' argument value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --scopes $ACR_REGISTRY_ID --role reader --query password --output tsv)
SP_APP_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output the service principal's credentials; use these in your services and
# applications to authenticate to the container registry.
echo "Service principal ID: $SP_APP_ID"
echo "Service principal password: $SP_PASSWD"
```

## <a name="use-an-existing-service-principal"></a>Usar uma entidade de serviço existente

Para conceder acesso de registro a uma entidade de serviço existente, você precisa atribuir uma nova função à entidade de serviço. Assim como ocorre ao criar uma nova entidade de serviço, você pode conceder acesso de pull, push e pull e proprietário.

O script a seguir usa o comando [az role assignment create][az-role-assignment-create] para conceder permissões *pull* a uma entidade de serviço especificada na variável `SERVICE_PRINCIPAL_ID`. Ajuste o valor `--role` se você desejar conceder um nível diferente de acesso.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_ID is the service principal's 'appId' or
# one of its 'servicePrincipalNames' values.
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_ID=<service-principal-ID>

# Populate value required for subsequent command args
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Assign the desired role to the service principal. Modify the '--role' argument
# value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
az role assignment create --assignee $SERVICE_PRINCIPAL_ID --scope $ACR_REGISTRY_ID --role reader
```

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create