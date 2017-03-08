## <a name="prerequisites"></a>Pré-requisitos

Obtenha uma [assinatura de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/) e instale a [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), caso ainda não tenha feito isso.

## <a name="create-the-scale-set"></a>Criar o conjunto de dimensionamento

Primeiro, crie um grupo de recursos para implantar o conjunto de dimensionamento em:

```azurecli
az group create --location westus --name myResourceGroup
```

Agora, crie seu conjunto de dimensionamento usando o comando `az vmss create`. O exemplo a seguir cria um conjunto de dimensionamento do Linux chamado `myvmss` no grupo de recursos chamado `myrg`:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

O exemplo a seguir cria um conjunto de dimensionamento do Windows com a mesma configuração:

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

Se desejar escolher uma imagem de sistema operacional diferente, você poderá ver as imagens disponíveis com o comando `az vm image list` ou `az vm image list --all`. Para ver as informações de conexão das VMs no conjunto de dimensionamento, use o comando `az vmss list_instance_connection_info`:

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```