---
title: Exemplo de Script CLI do Azure - Dimensionar um aplicativo Web manualmente usando a CLI do Azure 2.0 | Microsoft Docs
description: Exemplo de Script CLI do Azure - Dimensionar um aplicativo Web manualmente usando a CLI do Azure 2.0
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 251d9074-8fff-4121-ad16-9eca9556ac96
ms.service: app-service
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 2023e806d8ed5dc459af93533db6668659ba5898
ms.lasthandoff: 04/04/2017

---

# <a name="scale-a-web-app-manually"></a>Dimensionar manualmente um aplicativo Web

Nesse cenário, você aprenderá a criar um grupo de recursos, o aplicativo Web e o Plano do Serviço de Aplicativo. Em seguida, dimensionará o Plano do Serviço de Aplicativo de uma única instância para várias instâncias.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/scale-manual/scale-manual.sh "Escala manual")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos para criar um grupo de recursos, um aplicativo Web e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. Isso é como um farm de servidores para seu aplicativo Web do Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Cria um aplicativo web do Azure no plano do Serviço de Aplicativo. |
| [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update) | Atualiza as propriedades do Plano do Serviço de Aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../app-service-cli-samples.md).
