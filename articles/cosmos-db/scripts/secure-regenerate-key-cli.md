---
title: Script da CLI do Azure – regenerar chave de conta do Azure Cosmos DB
description: Exemplo de script da CLI do Azure – regenerar uma chave de conta do BD Cosmos do Azure
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.openlocfilehash: 38ebfde4b9b473f412ae667cdc6b36ea5e991608
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52958092"
---
# <a name="regenerate-an-azure-cosmos-db-account-key-using-the-azure-cli"></a>Regenere uma chave de conta do BD Cosmos do Azure usando a CLI do Azure

Este exemplo regenera qualquer tipo de chave de conta do BD Cosmos do Azure usando a CLI do Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-regenerate-keys/secure-cosmosdb-regenerate-keys.sh "Regenerate Azure Cosmos DB account keys")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Atualiza uma conta do BD Cosmos do Azure. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys) | Lista as chaves de acesso para a sua conta do Cosmos DB. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Regenera chaves de conta do Azure Cosmos DB. |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Banco de Dados Cosmos do Azure podem ser encontrados na [Documentação da CLI do Banco de Dados Cosmos do Azure](../cli-samples.md).
