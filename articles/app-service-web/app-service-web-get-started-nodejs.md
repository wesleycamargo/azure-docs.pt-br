---
title: Criar seu primeiro aplicativo Web do Node. js no Azure em cinco minutos | Microsoft Docs
description: "Saiba como é fácil executar aplicativos Web no Serviço de Aplicativo implantando um aplicativo Node.js de exemplo."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 412cc786-5bf3-4e1b-b696-6a08cf46501e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 89a9e29261e338aceb4ff6feb55cf344afeeb3d4
ms.lasthandoff: 03/21/2017


---
# <a name="create-your-first-nodejs-web-app-in-azure-in-five-minutes"></a>Criar seu primeiro aplicativo Web Node.js no Azure em cinco minutos
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Este Início Rápido ajuda a implantar seu primeiro aplicativo Web do Node.js para [o Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) em apenas alguns minutos.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, consulte o [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no Azure ao executar `az login` e acompanha as instruções na tela.
   
```azurecli
az login
```
   
## <a name="create-a-resource-group"></a>Criar um grupo de recursos   
Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Isso é onde você coloca todos os recursos do Azure que deseja gerenciar, como o aplicativo Web e seu back-end do Banco de Dados SQL.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Para ver quais são os valores possíveis para `---location`, use o comando `az appservice list-locations` da CLI do Azure.

## <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo
Crie um [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "Standard" que execute um contêiner do Linux. 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --is-linux --sku S1
```

## <a name="create-a-web-app"></a>Criar um aplicativo Web
Crie um aplicativo Web com um nome exclusivo no `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="configure-the-linux-container"></a>Configure o contêiner do Linux
Configure o contêiner do Linux para usar a imagem do Node.js 6.9.3 padrão.

```azurecli
az appservice web config update --node-version 6.9.3 --name <app_name> --resource-group myResourceGroup
```

## <a name="deploy-sample-application"></a>Implantar um aplicativo de exemplo
Implante um aplicativo Node.js de exemplo do GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Navegar até o aplicativo Web
Para ver o aplicativo em execução no Azure, execute este comando.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Parabéns, seu primeiro aplicativo Web do Node.js está em execução no Serviço de Aplicativo do Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Explore os [scripts da CLI de aplicativos Web](app-service-cli-samples.md) pré-criados.

