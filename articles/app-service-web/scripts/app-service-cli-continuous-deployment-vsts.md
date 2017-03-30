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
ms.date: 03/20/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a2009cb07ae2ae7cf716d77a7d9eac7482138ab1
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Criar um aplicativo Web com a implantação contínua do Visual Studio Team Services

Esse script de exemplo cria um aplicativo Web no Serviço de Aplicativo com recursos relacionados e, em seguida, configura a implantação contínua por meio de um repositório do Visual Studio Team Services. 

Se necessário, instale a CLI do Azure usando a instrução encontrada no [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Além disso, verifique se:

- Uma conexão com o Azure foi criado usando o comando `az login`.
- O código do aplicativo está em um repositório do Visual Studio Team Services de sua propriedade.
- Você [criou um token de acesso em sua conta do Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="create-app-sample"></a>Criar aplicativo de exemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "criar um aplicativo Web com a implantação contínua do Visual Studio Team Services")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Cria um aplicativo Web do Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associa a um aplicativo Web do Azure com um repositório Git ou Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Abra um aplicativo Web do Azure em um navegador. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Serviço de Aplicativo adicionais podem ser encontrados na [documentação do Serviço de Aplicativo do Azure](../app-service-cli-samples.md).
