---
title: "Implantar um aplicativo Go no Aplicativo Web para Contêineres do Azure | Microsoft Docs"
description: "Como implantar uma imagem do Docker executando um aplicativo Go para Aplicativos Web para Contêineres do Azure."
keywords: "serviço de aplicativo do azure, aplicativo web, go, docker, contêiner"
services: app-service
author: sptramer
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: sttramer
ms.custom: mvc
ms.openlocfilehash: dbe4d7bc6f5f1d83a079993c9616206fd82a1b9d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="deploy-a-go-app-in-azure-web-app-for-containers"></a>Implantar um aplicativo Go no Aplicativo Web para Contêineres do Azure

O Serviço de Aplicativo fornece pilhas de aplicativos predefinidas no Linux com suporte para versões específicas, como PHP 7.0 e Node.js 4.5. Também é possível usar uma imagem personalizada do Docker para executar seu aplicativo Web em uma pilha de aplicativos que ainda não foi definida no Azure. Este guia de início rápido mostra como utilizar um contêiner de Docker existente com um aplicativo Go e executá-lo no Serviço de Aplicativo do Azure. Crie o aplicativo Web usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-the-container"></a>Criar um aplicativo Web para o Contêiner

Crie um [aplicativo Web](../app-service-web-overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Não se esqueça de substituir `<app name>` por um nome globalmente exclusivo do aplicativo.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/appservice-go-quickstart
```

No comando anterior, `--deployment-container-image-name` aponta para a imagem pública do Hub do Docker [microsoft/appservice-go-quickstart](https://hub.docker.com/r/microsoft/appservice-go-quickstart).

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="get-data-from-the-apps-endpoint"></a>Obter dados do ponto de extremidade do aplicativo

Usando o comando `curl`, entre em contato com o ponto de extremidade de API REST fornecido pelo aplicativo do contêiner.

```bash
curl -X GET http://<app_name>.azurewebsites.net:8080/hello
```

```output
Hello world!
```

**Parabéns!** Você implantou uma imagem personalizada do Docker executando um aplicativo Go nos Aplicativos Web para Contêineres.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar uma imagem personalizada do Docker](tutorial-custom-docker-image.md)
