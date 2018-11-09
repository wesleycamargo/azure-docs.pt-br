---
title: Exemplo de script da CLI do Azure – criar cluster da IA do Lote comum arquivo de configuração | Microsoft Docs
description: Exemplo de script da CLI do Azure – crie um cluster de IA do Lote ao especificar definições de configuração em um arquivo JSON.
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
ms.date: 08/16/2018
ms.author: danlep
ms.openlocfilehash: 01281c0328fab85814ff93c73f9ea25e0d4c1b08
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232793"
---
# <a name="cli-example-create-a-batch-ai-cluster-using-a-cluster-configuration-file"></a>Exemplo da CLI: criar um cluster de IA do Lote usando um arquivo de configuração do cluster

Esse script demonstra como usar um arquivo de configuração JSON para especificar configurações para um cluster de IA do Lote. Use essas configurações em vez de parâmetros de linha de comando correspondentes para `az batchai cluster create`. Um arquivo de configuração é útil quando você precisa montar vários sistemas de arquivos em nós de cluster ou quiser usar uma configuração idêntica em vários clusters.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este guia exigirá que seja executada a CLI do Azure versão 2.0.38 ou posterior. Execute `az --version` para determinar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-config-file.sh "Create Batch AI cluster - configuration file")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute os comandos a seguir para remover os grupos de recursos e todos os recursos associados a eles.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Cria um compartilhamento de arquivos na conta de armazenamento. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Cria um espaço de trabalho do IA do Lote. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Cria um cluster do IA do Lote do Azure. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Mostra informações sobre um cluster de IA do Lote. |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
