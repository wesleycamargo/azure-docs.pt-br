---
title: "Criar uma Função do Azure que se conecta a um armazenamento Azure | Microsoft Docs"
description: "Amostra de script da CLI do Azure – Criar uma função do Azure que se conecta a um armazenamento Azure"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: functions
ms.assetid: 
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/20/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 3925c1a3d2c5a9fc3a82e8823dbdeffabeb38a3a
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017

---
# <a name="integrate-function-app-into-azure-storage-account"></a>Integrar o Aplicativo de funções à conta de armazenamento do Azure

Este exemplo de script cria um Aplicativo de funções e uma Conta de armazenamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

Este exemplo cria um Aplicativo de funções do Azure e adiciona a cadeia de conexão de armazenamento para uma configuração de aplicativo.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrar o Aplicativo de funções à conta de armazenamento do Azure")]


## <a name="clean-up-deployment"></a>Limpar implantação

Após a execução do exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo do Serviço de Aplicativo e todos os recursos relacionados:

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/#login) | Logon no Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Criar um grupo de recursos com local |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account) | Criar uma conta de armazenamento |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Criar um novo aplicativo de funções |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Limpar |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).

