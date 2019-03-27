---
title: Criar uma função do Azure que será implantada do Azure DevOps | Microsoft Docs
description: Criar um aplicativo de funções e implantar o código da função do Azure DevOps
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 07/03/2018
ms.topic: sample
ms.service: azure-functions
ms.custom: mvc
ms.openlocfilehash: 7fe68090773902248dbcdd63fbbdbbdb06b307cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852382"
---
# <a name="create-a-function-app-and-deploy-function-code-from-azure-devops"></a>Criar um aplicativo de funções e implantar o código da função do Azure DevOps

Este tópico mostra como usar o Azure Functions para criar um aplicativo de função [sem servidor](https://azure.microsoft.com/solutions/serverless/) usando o [plano de consumo](../functions-scale.md#consumption-plan). O aplicativo de funções, que é um contêiner para suas funções, é continuamente implantado de um repositório do Azure DevOps. 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

Para concluir este tópico, você deve ter:

* Um repositório do Azure DevOps que contém o projeto de aplicativo de função e ao qual você tem permissões administrativas.
* Um [PAT (token de acesso pessoal)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) para acessar o repositório do Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você preferir usar a CLI do Azure localmente, será necessário usar a versão 2.0 ou uma versão posterior. Para determinar a versão da CLI do Azure, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um aplicativo de funções do Azure e implanta o código da função do Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, conta de armazenamento, aplicativo de função e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Cria a conta de armazenamento necessária para o aplicativo de funções. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções no [plano de consumo](../functions-scale.md#consumption-plan) sem servidor. |
| [az functionapp deployment source config](https://docs.microsoft.com/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Associa um aplicativo de funções a um repositório Git ou Mercurial. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
