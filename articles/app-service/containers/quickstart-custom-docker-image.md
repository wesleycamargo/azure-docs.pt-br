---
title: "Executar uma imagem personalizada do Hub do Docker no Aplicativo Web para Contêineres | Microsoft Docs"
description: "Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres."
keywords: "serviço de aplicativo do azure, aplicativo web, linux, docker, contêiner"
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: c85f79cc14cdcecd2a05fc0ff91c4864b9fba277
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="run-a-custom-docker-hub-image-in-web-app-for-containers"></a>Executar uma imagem personalizada do Hub do Docker no Aplicativo Web para Contêineres

O Serviço de Aplicativo fornece pilhas de aplicativos predefinidas no Linux com suporte para versões específicas, como PHP 7.0 e Node.js 4.5. Também é possível usar uma imagem personalizada do Docker para implantar seu aplicativo Web em uma pilha de aplicativos que ainda não foi definida no Azure. Este guia de início rápido mostra como criar um aplicativo Web e implantar uma imagem do Docker baseada em Python nele. Crie o aplicativo Web usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Criar um Aplicativo Web para Contêiner

Crie um [aplicativo Web](../app-service-web-overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [az webapp create](/cli/azure/webapp#create). Não se esqueça de substituir `<app name>` por um nome exclusivo do aplicativo.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name elnably/dockerimagetest
```

No comando anterior, `--deployment-container-image-name` aponta para a imagem pública do Hub do Docker [https://hub.docker.com/r/elnably/dockerimagetest/](https://hub.docker.com/r/elnably/dockerimagetest/). Você pode inspecionar o conteúdo em [https://github.com/ahmedelnably/dockerimagetest](https://github.com/ahmedelnably/dockerimagetest).

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

Navegue para a URL a seguir usando o navegador da Web.

```bash
http://<app_name>.azurewebsites.net
```

![Aplicativo de exemplo em execução no Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Parabéns!** Você implantou uma imagem personalizada do Docker no aplicativo Web para Contêineres.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web Docker Python e PostgreSQL no Azure](tutorial-docker-python-postgresql-app.md)
