---
title: Exemplo de script da CLI do Azure – criar cluster da IA do Lote do Azure | Microsoft Docs
description: Exemplo de script da CLI do Azure – criar e gerenciar um cluster de IA do Lote de nós dedicados (máquinas virtuais)
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
ms.openlocfilehash: 643d2a5a600d018912f09d01f2e018100767f147
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410118"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-dedicated-nodes"></a>Exemplo de CLI: Criar e gerenciar um cluster de IA do Lote de nós dedicados

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Esse script demonstra alguns dos comandos disponíveis na CLI do Azure para criar e gerenciar um cluster da IA do Lote que consiste em nós dedicados (máquinas virtuais).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.38 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-dedicated.sh "Create Batch AI cluster - dedicated nodes")]

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
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Mostra informações sobre um cluster de IA do Lote. |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node#az-batchai-cluster-show) | Lista os nós em um cluster de IA do Lote. |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Redimensiona o cluster de IA do Lote.  |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
