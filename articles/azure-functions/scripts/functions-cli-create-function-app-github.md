---
title: "Criar um Aplicativo de funções e implantar o código da função do GitHub | Microsoft Docs"
description: "Exemplo de Script da CLI do Azure - Criar um Aplicativo de funções e implantar o código da função do GitHub"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f87cf7d300b4c2b89ad692aadcda958e9747c7f9
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017

---
# <a name="create-a-function-app-and-deploy-function-code-from-github"></a>Criar um Aplicativo de funções e implantar o código da função do GitHub

Este exemplo de script cria um aplicativo de funções usando o [plano de consumo](../functions-scale.md#consumption-plan) com seus recursos relacionados e, em seguida, implanta seu código de função de um repositório GitHub público (sem a implantação contínua). Para o fornecimento contínuo do código da função do GitHub, leia [Criar um aplicativo de funções e implantar continuamente do GitHub](functions-cli-create-function-app-github-continuous.md)

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um Aplicativo de funções do Azure e implanta o código da função do GitHub.

[!code-azurecli-interactive[principal](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "Criar um aplicativo de funções com implantação do GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Cada comando na tabela redireciona para a documentação específica do comando. Este script usa os seguintes comandos:

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Cria um Aplicativo de funções do Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associa um aplicativo de funções a um repositório Git ou Mercurial. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).

