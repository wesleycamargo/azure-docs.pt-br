---
title: Criar uma Função do Azure que se conecta a um Azure Cosmos DB | Microsoft Docs
description: Exemplo de script da CLI do Azure – Criar uma Função do Azure que se conecta a um BD do Azure Cosmos
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/03/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 6001fc5737ee901ab1ab5af0dd2f913f05380ac2
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161400"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Criar um Azure Function que se conecta a um Banco de Dados Cosmo do Azure

Este script de exemplo do Azure Functions cria um aplicativo de funções e conecta a função a um banco de dados do Azure Cosmos DB. A configuração de aplicativo criado que contém a conexão pode ser usada com um [Gatilho ou associação do Azure Cosmos DB](..\functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se usar a CLI localmente, certifique-se de que você esteja executando a versão 2.0 ou uma versão posterior da CLI do Azure. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um Aplicativo de funções do Azure e adiciona uma chave de acesso e um ponto de extremidade de BD do Cosmos para configurações do aplicativo.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos: cada comando na tabela contém links para a respectiva documentação específica.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Criar um grupo de recursos com local |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Criar uma conta de armazenamento |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções no [plano de consumo](../functions-scale.md#consumption-plan) sem servidor. |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az-cosmosdb-create) | Crie um banco de dados do Azure Cosmos DB. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).




