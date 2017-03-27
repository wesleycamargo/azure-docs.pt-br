---
title: "Exemplo de script da CLI do Azure - Criar um aplicativo Web com a implantação do GitHub | Microsoft Docs"
description: "Exemplo de script da CLI do Azure - Criar um aplicativo Web com a implantação do GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: ab4be9d9a03001789bed90e58d62fe1891608823
ms.lasthandoff: 03/11/2017

---


# <a name="create-a-web-app-with-deployment-from-github"></a>Criar um aplicativo Web com a implantação do GitHub

Este script de exemplo faz o seguinte usando a CLI do Azure 2.0:

* Crie um aplicativo Web no Serviço de Aplicativo do Azure na região Europa Ocidental do Azure.
* Implante o código do aplicativo Web do GitHub.
* Exiba o aplicativo Web do Azure implantado no navegador.

## <a name="prerequisites"></a>Pré-requisitos

* Execute `az login` para efetuar logon no Azure.
* Coloque o código de seu aplicativo Web em um repositório do GitHub.

> [!NOTE]
> Se você usar um repositório GitHub público que não seja de sua propriedade, o serviço de aplicativo implantará código por meio desse repositório GitHub, mas não poderá configurar a chave SSH e os webhooks necessário à implantação contínua.
>
>

## <a name="create-app-sample"></a>Criar aplicativo de exemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Criar um aplicativo Web com a implantação do GitHub")]

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
