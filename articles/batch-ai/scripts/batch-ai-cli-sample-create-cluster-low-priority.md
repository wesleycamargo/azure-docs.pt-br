---
title: Exemplo de script da CLI do Azure – criar cluster de baixa prioridade da IA do Lote | Microsoft Docs
description: Exemplo de script da CLI do Azure – criar e gerenciar um cluster de IA do Lote de nós de baixa prioridade (máquinas virtuais)
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: e1a37104a5dc6e89b147c8bb9e14b4eda36d8eef
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697842"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-low-priority-nodes"></a>Exemplo de CLI: Criar e gerenciar um cluster de IA do Lote de nós de baixa prioridade

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Esse script demonstra alguns dos comandos disponíveis na CLI do Azure para criar e gerenciar um cluster da IA do Lote que consiste em nós de baixa prioridade (máquinas virtuais).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.38 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-low-priority.sh "Create Batch AI cluster - low-priority nodes")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute os comandos a seguir para remover os grupos de recursos e todos os recursos associados a eles.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Cria um espaço de trabalho do IA do Lote. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Cria um cluster do IA do Lote do Azure. |
| [az batchai cluster show](/cli/azure/batchai/cluster) | Mostra informações sobre um cluster de IA do Lote. |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node) | Lista os nós em um cluster de IA do Lote. |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Redimensiona o cluster de IA do Lote.  |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
