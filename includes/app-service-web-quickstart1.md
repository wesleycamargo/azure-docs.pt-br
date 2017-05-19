## <a name="create-a-resource-group"></a>Criar um grupo de recursos

 Um [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) é um contêiner lógico no qual recursos como aplicativos Web e bancos de dados são gerenciados. Você implanta, atualiza e exclui os recursos em um grupo de recursos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create).

```azurecli
az group create --name myResourceGroup --location westeurope
```

Para ver os locais disponíveis, use o comando `az appservice list-locations`. Geralmente, você cria recursos em uma região perto de você.

## <a name="create-an-azure-app-service-plan"></a>Criar um Plano do Serviço de Aplicativo do Azure

Crie um [plano do Serviço de Aplicativo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO" com o comando [az appservice plan create](/cli/azure/appservice/plan#create).

[!INCLUDE [app-service-plan](app-service-plan.md)]

O comando a seguir cria um Plano do Serviço de Aplicativo denominado `quickStartPlan` usando o tipo de preço **Gratuito**.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Quando o Plano do Serviço de Aplicativo for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>Criar um aplicativo Web