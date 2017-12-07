---
title: "Criar uma Função do Azure que se conecta a um Azure Cosmos DB | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – Criar uma Função do Azure que se conecta a um BD do Azure Cosmos"
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 7e9ccd26e7c066189576903c39be63a421d1da23
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Criar um Azure Function que se conecta a um Banco de Dados Cosmo do Azure

Este exemplo de script cria um Aplicativo de funções do Azure e se conecta a um banco de dados do Banco de Dados Cosmos do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se usar a CLI localmente, certifique-se de que você esteja executando a versão 2.0 ou uma versão posterior da CLI do Azure. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um Aplicativo de funções do Azure e adiciona uma chave de acesso e um ponto de extremidade de BD do Cosmos para configurações do aplicativo.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos relacionados.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos: cada comando na tabela contém links para a respectiva documentação específica.

| Command | Observações |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Fazer logon no Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Criar um grupo de recursos com local |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account) | Criar uma conta de armazenamento |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Criar um novo aplicativo de funções |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_create) | Criar um banco de dados cosmosdb |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Limpar |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).




