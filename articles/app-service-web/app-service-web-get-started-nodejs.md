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
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 746f697076566ce3edd970336b005e53dc4d2d39
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-nodejs-web-app-in-azure-in-five-minutes"></a>Criar seu primeiro aplicativo Web Node.js no Azure em cinco minutos
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Este Início Rápido ajuda a implantar seu primeiro aplicativo Web do Node.js para [o Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md) em apenas alguns minutos.

Antes de começar o Início Rápido, verifique se [a CLI do Azure está instalada](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) em seu computador.

# <a name="create-a-nodejs-web-app"></a>Criar um aplicativo Web Node.js
2. Faça logon no Azure ao executar `az login` e acompanha as instruções na tela.
   
    ```azurecli
    az login
    ```
   
3. Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Isso é onde você coloca todos os recursos do Azure que deseja gerenciar, como o aplicativo Web e seu back-end do Banco de Dados SQL.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    Para ver quais são os valores possíveis para `---location`, use o comando `az appservice list-locations` da CLI do Azure.

3. Criar um [plano do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) “Standard”. A camada Standard é necessária para executar contêineres do Linux.

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku S1 --is-linux 
    ```

4. Crie um aplicativo Web com um nome exclusivo no `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Configure o contêiner do Linux para usar a imagem do Node.js 6.9.3 padrão.

    ```azurecli
    az appservice web config update --node-version 6.9.3 --name <app_name> --resource-group myResourceGroup
    ```

4. Implante um aplicativo Node.js de exemplo do GitHub.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git" --branch master --manual-integration 
    ```

5. Para ver o aplicativo em execução no Azure, execute este comando.

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Parabéns, seu primeiro aplicativo Web do Node.js está em execução no Serviço de Aplicativo do Azure.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

Explore os [scripts da CLI de aplicativos Web](app-service-cli-samples.md) pré-criados.

