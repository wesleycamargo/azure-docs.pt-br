---
title: Criar um aplicativo de hiperescala no Azure | Microsoft Docs
description: "Aprenda a usar os diferentes serviços do Azure para maximizar o desempenho de um aplicativo ASP.NET no Azure."
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: erikre
editor: 
ms.assetid: a4d49ac7-0f97-4997-84c5-cdb9c4465757
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f291186c6a68dea8aa00b846a2e6f3ad0d7996c
ms.openlocfilehash: eac9c5b0d8d0f7802d88e6f4f27d9d23c406e025
ms.contentlocale: pt-br
ms.lasthandoff: 04/28/2017


---
# <a name="build-a-hyper-scale-web-app-in-azure"></a>Criar um aplicativo Web de hiperescala no Azure

Este tutorial mostra como escalar horizontalmente um aplicativo Web ASP.NET no Azure para maximizar as solicitações do usuário.

Antes de começar este tutorial, verifique se [a CLI do Azure está instalada](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) no computador. Além disso, você precisa do [Visual Studio](https://www.visualstudio.com/vs/) no computador local para executar o aplicativo de exemplo.

## <a name="step-1---get-sample-application"></a>Etapa 1 – Obter aplicativo de exemplo
Nesta etapa, você deve configurar o projeto local do ASP.NET.

### <a name="clone-the-application-repository"></a>Clonar o repositório de aplicativo

Abra o terminal de linha de comando de sua escolha e `CD` para um diretório de trabalho. Execute os comandos a seguir para clonar o aplicativo de exemplo. 

```powershell
git clone https://github.com/cephalin/HighScaleApp.git
```

### <a name="run-the-sample-application-in-visual-studio"></a>Executar o aplicativo de exemplo no Visual Studio

Abra a solução no Visual Studio.

```powershell
cd HighScaleApp
.\HighScaleApp.sln
```

Digite `F5` para executar o aplicativo.

Esse aplicativo Web ASP.NET de exemplo é originado do modelo padrão, além de persistir sessões do usuário e usar o cache de saída. Observe `HighScaleApp\Controllers\HomeController.cs`. O método `Index()` adiciona uma parte dos dados à sessão.

```csharp
Session.Add("visited", "true"); 
```

E os métodos `About()` e `Contact()` armazenam a saída em cache.

```csharp
[OutputCache(Duration = 60)]
```

## <a name="step-2---deploy-to-azure"></a>Etapa 2 – Implantar no Azure
Nesta etapa, você cria um aplicativo Web do Azure e implanta o aplicativo ASP.NET de exemplo nele.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos   
Use [az group create](https://docs.microsoft.com/cli/azure/group#create) para criar um [grupo de recursos](../azure-resource-manager/resource-group-overview.md) na região Europa Ocidental. Um grupo de recursos é onde você coloca todos os recursos do Azure que quer gerenciar, como o aplicativo Web e qualquer back-end do Banco de Dados SQL.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Para ver quais são os possíveis valores que podem ser usados para `---location`, use o comando [az appservice list-locations](https://docs.microsoft.com/en-us/cli/azure/appservice#list-locations).

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo
Use [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) para criar um [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "B1". 

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1
```

Um plano do Serviço de Aplicativo é uma unidade de escala, que pode incluir qualquer número de aplicativos que você desejar escalar vertical ou horizontalmente juntos na mesma infraestrutura de Serviço de Aplicativo. Cada plano também recebe um [tipo de preço](https://azure.microsoft.com/en-us/pricing/details/app-service/). Tipos mais alto incluem hardware melhor e mais recursos, como mais instâncias de escala horizontal.

Neste tutorial, B1 é tipo mínimo, que permite escalar horizontalmente para três instâncias. Sempre que quiser, você pode mover seu aplicativo para cima ou para baixo no tipo de preço executando o comando [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update). 

### <a name="create-a-web-app"></a>Criar um aplicativo Web
Use [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create) para criar um aplicativo Web com um nome exclusivo em `$appName`.

```azurecli
$appName = "<replace-with-a-unique-name>"
az appservice web create --name $appName --resource-group myResourceGroup --plan myAppServicePlan
```

### <a name="set-deployment-credentials"></a>Definir credenciais de implantação
Use [az appservice web deployment user set](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/user#set) para definir as credenciais no nível de conta para o Serviço de Aplicativo.

```azurecli
az appservice web deployment user set --user-name <letters-numbers> --password <mininum-8-char-captital-lowercase-letters-numbers>
```

### <a name="configure-git-deployment"></a>Configurar a implantação do Git
Use [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) para configurar a implantação do Git local.

```azurecli
az appservice web source-control config-local-git --name $appName --resource-group myResourceGroup
```

Esse comando fornece uma saída semelhante a esta:

```json
{
  "url": "https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git"
}
```

Use a URL retornada para configurar o Git remoto. O comando a seguir usa o exemplo de saída anterior.

```powershell
git remote add azure https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-the-sample-application"></a>Implantar o aplicativo de exemplo
Agora, você está pronto para implantar o aplicativo de exemplo. Execute `git push`.

```powershell
git push azure master
```

Quando a senha for solicitada, use a senha que você especificou quando executou `az appservice web deployment user set`.

### <a name="browse-to-azure-web-app"></a>Navegar até o aplicativo Web do Azure
Use [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) para ver seu aplicativo em execução ao vivo no Azure. Execute este comando.

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-3---connect-to-redis"></a>Etapa 3 – Conectar-se ao Redis
Nesta etapa, você configura o Cache Redis do Azure como um cache externo, colocado no seu aplicativo Web do Azure. É possível utilizar rapidamente o Redis para armazenar em cache a saída da página. Além disso, ao escalar horizontalmente os aplicativos Web posteriormente, o Redis ajuda a persistir as sessões do usuário em várias instâncias de modo confiável.

### <a name="create-an-azure-redis-cache"></a>Criar um Cache Redis do Azure
Use [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) para criar um Cache Redis do Azure e salvar a saída JSON. Use um nome exclusivo em `$cacheName`.

```powershell
$cacheName = "<replace-with-a-unique-cache-name>"
$redis = (az redis create --name $cacheName --resource-group myResourceGroup --location "West Europe" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

### <a name="configure-the-application-to-use-redis"></a>Configurar o aplicativo para usar o Redis
Formate a cadeia de conexão para o cache.

```powershell
$connstring = "$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False"
$connstring 
```

A segunda linha deve fornecer uma saída parecida com esta:

```
mycachename.redis.cache.windows.net:6380,password=/rQP/TLz1mrEPpmh9b/gnfns/t9vBRXqXn3i1RwBjGA=,ssl=True,abortConnect=False
```

No Visual Studio, crie um arquivo de configuração da Web na raiz do projeto chamado `redis.config` e cole nele o código a seguir. Em `value`, use a cadeia de conexão da saída do PowerShell.

```xml
<appSettings>
  <add key="RedisConnection" value="your-azure-redis-cache-connection-string"/>
</appSettings>
```

Examinando o arquivo `.gitignore` no repositório Git, você verá que esse arquivo foi excluído do controle de origem. Dessa forma, as informações confidenciais são mantidas em segurança. 

Abra `Web.config`. Observe o elemento `<appSettings file="redis.config">`, que obtém a configuração que você criou em `redis.config`. 

Localize a seção comentada que inclui `<sessionState>` e `<caching>`. Remova os comentários dessa seção.

![](./media/app-service-web-tutorial-hyper-scale-app/redisproviders.png)

Esse código procura a cadeia de conexão do Redis que você definiu em `RedisConnection`. 

Agora, o aplicativo usa o Redis para gerenciar sessões e o caching. Digite `F5` para executar o aplicativo. Se desejar, você pode baixar um cliente de gerenciamento do Redis para visualizar os dados que agora são salvos no cache.

### <a name="configure-the-connection-string-in-azure"></a>Configurar a cadeia de conexão no Azure

Para que o aplicativo funcione no Azure, você precisa configurar a mesma cadeia de conexão do Redis no aplicativo Web do Azure. Uma vez que `redis.config` não é mantido no controle de origem, ele não é implantado no Azure quando você executa a implantação do Git.

Use [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) para adicionar a cadeia de conexão com o mesmo nome (`RedisConnection`).

az appservice web config appsettings update --settings "RedisConnection=$connstring" --name $appName --resource-group myResourceGroup

Lembre-se de que `$connstring` contém a cadeia de conexão formatada.

### <a name="redeploy-the-application-to-azure"></a>Reimplantar o aplicativo no Azure
Use os comandos Git para enviar por push suas alterações ao Azure

```bash
git add .
git commit -m "now use Redis providers"
git push azure master
```

Quando a senha for solicitada, use a senha que você especificou quando executou `az appservice web deployment user set`.

### <a name="browse-to-the-azure-web-app"></a>Navegar até o aplicativo Web do Azure
Use [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) para ver as alterações em tempo real no Azure.

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-4---scale-to-multiple-instances"></a>Etapa 4 – Escalar para várias instâncias
O plano do Serviço de Aplicativo é a unidade de escala para seus aplicativos Web do Azure. Para escalar horizontalmente o aplicativo Web, escale o plano do Serviço de Aplicativo.

Use [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) para escalar horizontalmente o plano do Serviço de Aplicativo para três instâncias, que é o número máximo permitido pelo tipo de preço B1. Lembre-se de que B1 é o tipo de preço que você escolheu quando criou anteriormente o plano do Serviço de Aplicativo. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --number-of-workers 3 
```

## <a name="step-5---scale-geographically"></a>Etapa 5 – Escalar geograficamente
Ao escalar geograficamente, você executa o aplicativo em várias regiões da nuvem do Azure. Essa configuração balanceia a carga do aplicativo com base na localização geográfica e reduz o tempo de resposta colocando o aplicativo mais próximo dos navegadores cliente.

Nessa etapa, você escala o aplicativo Web ASP.NET para uma segunda região com o [Gerenciador de Tráfego do Azure](https://docs.microsoft.com/en-us/azure/traffic-manager/). No final da etapa, você terá um aplicativo Web em execução na Europa Ocidental (já criado) e um aplicativo Web em execução no Sudeste Asiático (ainda não criado). Ambos os aplicativos serão atendidos pela mesma URL do Gerenciador de Tráfego.

### <a name="scale-up-the-europe-app-to-standard-tier"></a>Escalar verticalmente o aplicativo na Europa para o tipo Standard
No Serviço de Aplicativo, a integração ao Gerenciador de Tráfego do Azure exige o tipo de preço Standard. Use [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) para escalar verticalmente o plano do Serviço de Aplicativo para S1. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --sku S1
```
### <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gerenciador de Tráfego 
Use [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#create) para criar um perfil do Gerenciador de Tráfego e adicioná-lo ao grupo de recursos. Use um nome DNS exclusivo em $dnsName.

```azurecli
$dnsName = "<replace-with-unique-dns-name>"
az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name $dnsName
```

> [!NOTE]
> `--routing-method Performance` especifica que esse perfil [roteia o tráfego do usuário para o ponto de extremidade mais próximo](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="get-the-resource-id-of-the-europe-app"></a>Obter a ID de recurso do aplicativo na Europa
Use [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show) para obter a ID de recurso do aplicativo Web.

```azurecli
$appId = az appservice web show --name $appName --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-europe-app"></a>Adicionar um ponto de extremidade do Gerenciador de Tráfego para o aplicativo na Europa
Use [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create) para adicionar um ponto de extremidade ao seu perfil do Gerenciador de Tráfego e use a ID de recurso do aplicativo Web como o destino.

```azurecli
az network traffic-manager endpoint create --name myWestEuropeEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appId
```

### <a name="get-the-traffic-manager-endpoint-url"></a>Obter a URL do ponto de extremidade do Gerenciador de Tráfego
O perfil do Gerenciador de Tráfego agora tem um ponto de extremidade que aponta para seu aplicativo Web existente. Use [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#show) para obter sua URL. 

```azurecli
az network traffic-manager profile show --name myTrafficManagerProfile --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
```

Copie a saída no navegador. Você deve ver o aplicativo Web novamente.

### <a name="create-an-azure-redis-cache-in-asia"></a>Criar um Cache Redis do Azure na Ásia
Agora, você replica seu aplicativo Web na região Sudeste Asiático. Para começar, use [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) para criar um segundo Cache Redis do Azure no Sudeste Asiático. Esse cache precisa ser colocado com seu aplicativo na Ásia.

```powershell
$redis = (az redis create --name $cacheName-asia --resource-group myResourceGroup --location "Southeast Asia" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

`--name $cacheName-asia` dá ao cache o nome do cache da Europa Ocidental, com o sufixo `-asia`.

### <a name="create-an-app-service-plan-in-asia"></a>Criar um plano do Serviço de Aplicativo na Ásia
Use [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) para criar um segundo plano do Serviço de Aplicativo na região Sudeste Asiático usando o mesmo tipo S1 que o plano da Europa Ocidental.

```azurecli
az appservice plan create --name myAppServicePlanAsia --resource-group myResourceGroup --location "Southeast Asia" --sku S1
```

### <a name="create-a-web-app-in-asia"></a>Criar um aplicativo Web na Ásia
Use [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create) para criar um segundo aplicativo Web.

```azurecli
az appservice web create --name $appName-asia --resource-group myResourceGroup --plan myAppServicePlanAsia
```

`--name $appName-asia` dá ao aplicativo o nome do aplicativo da Europa Ocidental, com o sufixo `-asia`.

### <a name="configure-the-connection-string-for-redis"></a>Configurar a cadeia de conexão para Redis
Use [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) para adicionar ao aplicativo Web a cadeia de conexão para o cache do Sudeste Asiático.

az appservice web config appsettings update --settings "RedisConnection=$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False" --name $appName-asia --resource-group myResourceGroup

### <a name="configure-git-deployment-for-the-asia-app"></a>Configurar a implantação do Git para o aplicativo na Ásia.
Use [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) a fim de configurar a implantação do Git local para o segundo aplicativo Web.

```azurecli
az appservice web source-control config-local-git --name $appName-asia --resource-group myResourceGroup
```

Esse comando fornece uma saída semelhante a esta:

```json
{
  "url": "https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git"
}
```

Use a URL retornada de modo a configurar um segundo Git remoto para o repositório local. O comando a seguir usa o exemplo de saída anterior.

```bash
git remote add azure-asia https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-your-sample-application"></a>Implantar o aplicativo de exemplo
Execute `git push` para implantar o aplicativo de exemplo no segundo Git remoto. 

```bash
git push azure-asia master
```

Quando a senha for solicitada, use a senha que você especificou quando executou `az appservice web deployment user set`.

### <a name="browse-to-the-asia-app"></a>Navegar até o aplicativo na Ásia
Use [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) para verificar se o aplicativo está em execução em tempo real no Azure.

```azurecli
az appservice web browse --name $appName-asia --resource-group myResourceGroup
```

### <a name="get-the-resource-id-of-the-asia-app"></a>Obter a ID de recurso do aplicativo na Ásia
Use [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show) para obter a ID de recurso do aplicativo Web no Sudeste Asiático.

```azurecli
$appIdAsia = az appservice web show --name $appName-asia --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-asia-app"></a>Adicionar um ponto de extremidade do Gerenciador de Tráfego para o aplicativo na Ásia
Use [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create) para adicionar um segundo ponto de extremidade ao perfil do Gerenciador de Tráfego.

```azurecli
az network traffic-manager endpoint create --name myAsiaEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appIdAsia
```

### <a name="add-region-identifier-to-web-apps"></a>Adicionar identificador de região aos aplicativos Web
Use [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) para adicionar uma variável de ambiente específica da região.

```azurecli
az appservice web config appsettings update --settings "Region=West Europe" --name $appName --resource-group myResourceGroup
az appservice web config appsettings update --settings "Region=Southeast Asia" --name $appName-asia --resource-group myResourceGroup
```

O código do seu aplicativo já usa essa configuração de aplicativo. Observe `HighScaleApp\Views\Home\Index.cshtml`.

### <a name="complete"></a>Concluído!

Agora, tente acessar a URL do seu perfil do Gerenciador de Tráfego nos navegadores em diferentes regiões geográficas. Os navegadores cliente da Europa devem mostrar "ASP.NET Europa Ocidental" e o navegador cliente da Ásia deve mostrar "ASP.NET Sudeste Asiático".

## <a name="more-resources"></a>Mais recursos

