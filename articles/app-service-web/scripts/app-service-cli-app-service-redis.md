---
title: Exemplo de script da CLI do Azure - Conectar um aplicativo Web a um cache redis | Microsoft Docs
description: Exemplo de script da CLI do Azure - Conectar um aplicativo Web a um cache redis
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f94cfaf5a2c8585dadf9fec74feb6d8875a1b3e4
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017

---

# <a name="connect-a-web-app-to-a-redis-cache"></a>Conectar um aplicativo Web a um cache redis

Nesse cenário, você aprenderá a criar um Cache Redis do Azure e um aplicativo Web do Azure. Em seguida, você vinculará o cache redis ao aplicativo Web usando as configurações do aplicativo.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[principal](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Cache Redis do Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, o aplicativo Web, o cache redis e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. Isso é como um farm de servidores para seu aplicativo Web do Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Cria um aplicativo web do Azure no plano do Serviço de Aplicativo. |
| [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create) | Crie uma nova instância do Cache Redis. É aqui que os dados serão armazenados. |
| [az redis list-keys](https://docs.microsoft.com/en-us/cli/azure/redis#list-keys) | Lista as teclas de acesso para a instância do cache redis. |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Cria ou atualiza uma configuração de aplicativo para um aplicativo Web do Azure. As configurações do aplicativo são expostas como variáveis do ambiente para seu aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../app-service-cli-samples.md).

