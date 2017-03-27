---
title: "Exemplo de script da CLI do Azure - Criar um aplicativo Web com a implantação contínua do Visual Studio Team Services | Microsoft Docs"
description: "Exemplo de script da CLI do Azure - Criar um aplicativo Web com a implantação contínua do Visual Studio Team Services"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 5fce9171dc249445d78cee248fb2a7ec62a3dabb
ms.lasthandoff: 03/11/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Criar um aplicativo Web com a implantação contínua do Visual Studio Team Services

Este script de exemplo faz o seguinte usando a CLI do Azure 2.0: 

* Crie um aplicativo Web no Serviço de Aplicativo do Azure na região Europa Ocidental do Azure. 
* Implante o código do aplicativo Web do Visual Studio Team Services.
* Exiba o aplicativo Web do Azure implantado no navegador.

## <a name="prerequisites"></a>Pré-requisitos

* Execute `az login` para efetuar logon no Azure.
* Coloque o código de seu aplicativo Web em um repositório do Visual Studio Team Services.
* Para um repositório do Visual Studio Team Services de sua propriedade, [crie um token de acesso](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).

## <a name="create-app-sample"></a>Criar aplicativo de exemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "criar um aplicativo Web com a implantação contínua do Visual Studio Team Services")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Cria um aplicativo Web do Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associa a um aplicativo Web do Azure com um repositório Git ou Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Abra um aplicativo Web do Azure em um navegador. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../app-service-cli-samples.md).
