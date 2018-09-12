---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/07/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 1af4506bc1029efcb6c58ff955087ad0e1311fbc
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095685"
---
Crie um [aplicativo Web](../articles/app-service/containers/app-service-linux-intro.md) no plano do Serviço de Aplicativo `myAppServicePlan`. 

No Cloud Shell, é possível usar o comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). No exemplo a seguir, substitua `<app_name>` por um nome do aplicativo exclusivo globalmente (os caracteres válidos são `a-z`, `0-9` e `-`). A execução é predefinida para `PYTHON|3.7`. Para ver todos os tempos de execução com suporte, execute [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "PYTHON|3.7" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "PYTHON|3.7" --deployment-local-git
```

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

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

Você criou um novo aplicativo Web vazio com a implantação do Git habilitada.

> [!NOTE]
> A URL do Git remoto é mostrada na propriedade `deploymentLocalGitUrl` com o formato `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Salve essa URL, pois você precisará dela mais tarde.
>
