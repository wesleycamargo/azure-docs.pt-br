---
title: "Arquivo de inclusão"
description: "Arquivo de inclusão"
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 70548db9ef98cc8fa59ebc11c1371325e63e02fc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
No Cloud Shell, crie um [aplicativo Web](../articles/app-service/app-service-web-overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan`. Você pode fazer isso usando o comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). No exemplo a seguir, substitua *\<app_name>* por um nome do aplicativo exclusivo globalmente (os caracteres válidos são `a-z`, `0-9` e `-`). 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Você criou um aplicativo Web vazio com a implantação do git habilitada.

> [!NOTE]
> A URL do Git remoto é mostrada na propriedade `deploymentLocalGitUrl` com o formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Salve essa URL, pois você precisará dela mais tarde.
>

Navegue até o aplicativo Web recém-criado.

```
http://<app_name>.azurewebsites.net
```

Seu novo aplicativo Web deve ficar assim:
