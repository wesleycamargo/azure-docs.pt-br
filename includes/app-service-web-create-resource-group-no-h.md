No Cloud Shell, crie um grupo de recursos com o comando [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *Europa Ocidental*. Para ver todos os locais com suporte para o Serviço de Aplicativo, execute o comando `az appservice list-locations`.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Em geral, você cria seu grupo de recursos e os recursos em uma região próxima a você. 
