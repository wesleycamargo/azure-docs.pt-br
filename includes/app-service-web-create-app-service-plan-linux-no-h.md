No Cloud Shell, crie um plano do Serviço de Aplicativo no grupo de recursos com o comando [az appservice plan create](/cli/azure/appservice/plan#create).

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

O exemplo a seguir cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` no tipo de preço **Standard** (`--sku S1`) e em um contêiner do Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Quando o Plano do Serviço de Aplicativo for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```