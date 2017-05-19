---
title: "Script da CLI do Azure – obter chaves de conta para o BD Cosmos do Azure | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – obter chaves da conta para o BD Cosmos do Azure"
services: cosmosdb
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4c0ad9c4fe4a3a7f67904998718eb66eb15d816b
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="get-account-keys-for-azure-cosmos-db-using-the-azure-cli"></a>Obtenha chaves de conta para o BD Cosmos do Azure usando a CLI do Azure

Este exemplo obtém chaves de conta para qualquer tipo de conta do BD Cosmos do Azure.  

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/cosmosdb/scale-cosmosdb-get-account-key/secure-cosmosdb-get-account-key.sh?highlight=22-25 "Obter chaves de conta do BD Cosmos do Azure")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | Atualiza uma conta do BD Cosmos do Azure. |
| [az cosmosdb list-keys](/cli/azure/sql/server#create) | Cria um servidor lógico que hospeda o Banco de Dados SQL. |
| [az group delete](/cli/azure/resource#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do BD Cosmos do Azure podem ser encontrados na [Documentação da CLI do BD Cosmos do Azure](../cli-samples.md).

