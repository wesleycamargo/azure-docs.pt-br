---
title: Criar aplicativo do Docker/Go no Linux – Serviço de Aplicativo do Azure
description: Como implantar uma imagem do Docker executando um aplicativo Go para o Aplicativo Web para Contêineres do Azure.
keywords: serviço de aplicativo do azure, aplicativo web, go, docker, contêiner
services: app-service
author: msangapu
manager: jeconnoc
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 3335163c15319e39858003032ae7113c9c26a3fd
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438573"
---
# <a name="deploy-a-dockergo-web-app-in-web-app-for-containers"></a>Implantar um aplicativo Web Docker/Go no Aplicativo Web para Contêineres

[O Serviço de Aplicativo do Linux](app-service-linux-intro.md) fornece pilhas de aplicativos predefinidos em Linux com suporte para linguagens como .NET, PHP, Node.js e outros. Também é possível usar uma imagem personalizada do Docker para executar seu aplicativo Web em uma pilha de aplicativos que ainda não foi definida no Azure. Este guia de início rápido mostra como criar um aplicativo Web e implantar uma imagem do Go a partir do Hub do Docker. Crie o aplicativo Web usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Aplicativo de exemplo em execução no Azure](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Crie um [aplicativo Web](../overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Não se esqueça de substituir `<app name>` por um nome globalmente exclusivo do aplicativo.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

No comando anterior, `--deployment-container-image-name` aponta para a imagem pública do Hub do Docker [microsoft/azure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/).

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

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

```bash
http://<app_name>.azurewebsites.net/hello
```

![Aplicativo de exemplo em execução no Azure](media/quickstart-docker-go/hello-world-in-browser.png)

**Parabéns!** Você implantou uma imagem personalizada do Docker executando um aplicativo Go no Aplicativo Web para Contêineres.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar uma imagem personalizada do Docker](tutorial-custom-docker-image.md)