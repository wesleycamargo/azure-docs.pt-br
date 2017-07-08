---
title: "Criar um Aplicativo de funções e implantar o código da função do Visual Studio Team Services | Microsoft Docs"
description: "Criar um Aplicativo de funções e implantar o código da função do Visual Studio Team Services"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/28/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 2ef177b55ad7ffd351156821f429e6ff8fbeccc7
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017

---
# <a name="create-an-app-service"></a>Criar um Serviço de Aplicativo

Nesse cenário, você aprenderá a criar um Aplicativo de funções usando o [plano consumo](../functions-scale.md#consumption-plan) com seus recursos relacionados, e a implantar continuamente o código de sua função de um repositório do VSTS (Visual Studio Team Services). Nesta amostra, você precisará de:

* Um repositório do VSTS com um código de funções, para o qual você tem permissões administrativas.
* Um [PAT (Token de Acesso Pessoal)](https://help.github.com/articles/creating-an-access-token-for-command-line-use) para sua conta do GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um Aplicativo de funções do Azure e implanta o código da função do Visual Studio Team Services.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Serviço do Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos para criar um grupo de recursos, um aplicativo Web, o DocumentDB e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Cria um Plano do Serviço de Aplicativo. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associa um aplicativo de funções a um repositório Git ou Mercurial. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).

