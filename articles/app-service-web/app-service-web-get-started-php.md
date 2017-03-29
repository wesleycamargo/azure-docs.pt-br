---
title: Criar seu primeiro aplicativo Web PHP no Azure em cinco minutos | Microsoft Docs
description: "Saiba como é fácil executar os aplicativos Web no Serviço de Aplicativo implantando um aplicativo PHP de exemplo."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: afecc8997631bf507c797e56a9e3fc0d1df27614
ms.lasthandoff: 03/21/2017


---
# <a name="create-your-first-php-web-app-in-azure-in-five-minutes"></a>Criar seu primeiro aplicativo Web PHP no Azure em cinco minutos
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Este Início Rápido ajuda a implantar seu primeiro aplicativo Web PHP para o [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) em apenas alguns minutos.

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
Configurar o contêiner do Linux para usar a imagem PHP 7.0.6 padrão.

```azurecli
az appservice web config update --php-version 7.0.6 --name <app_name> --resource-group myResourceGroup
```
## <a name="deploy-sample-application"></a>Implantar um aplicativo de exemplo
Implante um aplicativo PHP de exemplo do GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-php-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Navegar até o aplicativo Web
Para ver o aplicativo em execução no Azure, execute este comando.

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Parabéns, seu primeiro aplicativo Web PHP está em execução no Serviço de Aplicativo do Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Explore os [scripts da CLI de aplicativos Web](app-service-cli-samples.md) pré-criados.

