---
title: Criar seu primeiro aplicativo Web Java no Azure em cinco minutos | Microsoft Docs
description: "Saiba como é fácil executar aplicativos Web no Serviço de Aplicativo implantando um aplicativo de exemplo."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: e48e03e86a325b8f39809a49cdd19820dfa78bdc
ms.lasthandoff: 03/10/2017


---
# <a name="create-your-first-java-web-app-in-azure-in-five-minutes"></a>Criar seu primeiro aplicativo Web Java no Azure em cinco minutos
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Este Início Rápido ajuda a implantar seu primeiro aplicativo Web Java para [o Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) em apenas alguns minutos.

Antes de começar o Início Rápido, verifique se [a CLI do Azure está instalada](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) em seu computador.

## <a name="create-a-java-web-app-in-azure"></a>Criar um aplicativo Web do Java no Azure
2. Faça logon no Azure ao executar `az login` e acompanha as instruções na tela.
   
    ```azurecli
    az login
    ```
   
3. Crie um [grupos de recursos](../azure-resource-manager/resource-group-overview.md). Isso é onde você coloca todos os recursos do Azure que deseja gerenciar, como o aplicativo Web e seu back-end do Banco de Dados SQL.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    Para ver quais são os valores possíveis para `---location`, use o comando `az appservice list-locations` da CLI do Azure.

3. Criar um [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO". 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. Crie um novo aplicativo Web com um nome exclusivo no `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Implante um aplicativo Java de exemplo do GitHub.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/azure-appservice-samples/JavaCoffeeShopTemplate.git" --branch master --manual-integration 
    ```


5. Para ver o aplicativo em execução no Azure, execute este comando.

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Parabéns, seu primeiro aplicativo Web Java está em execução no Serviço de Aplicativo do Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Explore os [scripts da CLI de aplicativos Web](app-service-cli-samples.md) pré-criados.

