---
title: Criar uma Função do Azure que se conecta a um armazenamento Azure | Microsoft Docs
description: Amostra de script da CLI do Azure – Criar uma função do Azure que se conecta a um armazenamento Azure
services: functions
documentationcenter: functions
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: aa25c0e636c36422cb6b576d0ca8ebc899b2a2dc
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163066"
---
# <a name="create-a-function-app-that-connects-to-an-azure-storage-account"></a>Criar um aplicativo de funções que se conecta a uma conta de armazenamento do Azure

Este script de exemplo do Azure Functions cria um aplicativo de funções e conecta a função a uma conta de Armazenamento do Azure. A configuração do aplicativo criado que contém a conexão pode ser usada com um [gatilho ou associação de armazenamento](..\functions-bindings-storage-blob.md). 

[!INCLUDE [upgrade runtime](../../../includes/functions-cli-version-note.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se usar a CLI localmente, certifique-se de que você esteja executando a versão 2.0 ou uma versão posterior da CLI do Azure. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um Aplicativo de funções do Azure e adiciona a cadeia de conexão de armazenamento para uma configuração de aplicativo.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]


## <a name="clean-up-deployment"></a>Limpar a implantação

Após executar o exemplo de script, execute o comando a seguir para remover o grupo de recursos e todos os recursos relacionados:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Crie um grupo de recursos com local. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-create) | Criar uma conta de armazenamento. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções no [plano de consumo](../functions-scale.md#consumption-plan) sem servidor. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
