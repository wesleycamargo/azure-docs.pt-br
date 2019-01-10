---
title: Script da CLI do Azure – criar uma política de failover para alta disponibilidade
description: Exemplo de script da CLI do Azure – criar uma política de failover para alta disponibilidade
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/26/2018
ms.reviewer: sngun
ms.openlocfilehash: dc7a8030faf649e36136f6b1da366b4745f617b3
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042862"
---
# <a name="create-a-failover-policy-for-high-availability-using-the-azure-cli"></a>Crie uma política de failover para alta disponibilidade usando a CLI do Azure

Este exemplo de script da CLI cria uma conta do BD Cosmos do Azure e, em seguida, a configura para alta disponibilidade.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/high-availability-cosmosdb-configure-failover/high-availability-cosmosdb-configure-failover.sh "Create an Azure Cosmos DB failover policy")]

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
| [az cosmosdb create](/cli/azure/sql/server#az-sql-server-create) | Cria uma conta do BD Cosmos do Azure. |
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Atualiza uma conta do BD Cosmos do Azure. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Banco de Dados Cosmos do Azure podem ser encontrados na [Documentação da CLI do Banco de Dados Cosmos do Azure](../cli-samples.md).
