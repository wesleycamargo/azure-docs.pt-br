---
title: "Amostra de script da CLI do Azure – calcular o tamanho do contêiner de blob | Microsoft Docs"
description: "Calcule o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho dos blobs no contêiner."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.openlocfilehash: f9213018969ab47ce2e78d8c119f22dedaff9452
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcular o tamanho de um contêiner de Armazenamento de Blobs

Este script calcula o tamanho de um contêiner no Armazenamento de Blobs do Azure, totalizando o tamanho dos blobs no contêiner.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Este script do CLI fornece um tamanho estimado para o contêiner e não deve ser usado para cálculos de cobrança.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Execute o comando a seguir para remover o grupo de recursos, o contêiner e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para calcular o tamanho do contêiner de Armazenamento de Blobs. Cada item em que a tabela contém links para a documentação específica do comando.

| Get-Help | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage blob upload](/cli/azure/storage/account#az_storage_account_create) | Carrega arquivos locais para um contêiner de Armazenamento de Blobs do Azure. |
| [az storage blob list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | Lista os blobs em um contêiner de Armazenamento de Blobs do Azure. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure/overview).

Amostras adicionais de script CLI de armazenamento podem ser encontradas nas [Amostras de CLI do Azure para Armazenamento de Blobs do Azure](../blobs/storage-samples-blobs-cli.md).
