---
title: "Exemplo de script da CLI do Azure - Criar um aplicativo Web com a implantação contínua do GitHub | Microsoft Docs"
description: "Exemplo de script da CLI do Azure - Criar um aplicativo Web com a implantação contínua do GitHub"
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
ms.openlocfilehash: 6ba7dacb9a8828495c9ec15b46020a493d406096
ms.lasthandoff: 03/11/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Criar um aplicativo web com a implantação contínua do GitHub

Este script de exemplo cria um aplicativo Web no serviço de aplicativo com seus recursos relacionados e, em seguida, define a implantação contínua de um repositório GitHub. Para implantação do GitHub sem a implantação contínua, veja [Criar um aplicativo Web e implantar o código do GitHub](app-service-cli-deploy-github.md).

Antes de executar este script, verifique o seguinte:

- Uma conexão com o Azure foi criado usando o comando `az login`.
- O código do aplicativo está em um repositório GitHub público ou privado que você possui.
- Você [criou um token de acesso em sua conta do GitHub](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="create-app-sample"></a>Criar aplicativo de exemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/deploy-github-continuous/deploy-github-continuous.sh?highlight=3-4 "Criar um aplicativo web com a implantação contínua do GitHub")]

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
