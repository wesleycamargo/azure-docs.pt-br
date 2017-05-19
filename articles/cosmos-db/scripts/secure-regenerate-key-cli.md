---
title: "Script da CLI do Azure – regenerar chave de conta do BD Cosmos do Azure | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – regenerar uma chave de conta do BD Cosmos do Azure"
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
ms.openlocfilehash: ffdf78a42d5ff6ec2500167ecf1f31f17db3b830
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="regenerate-an-azure-cosmos-db-account-key-using-the-azure-cli"></a>Regenere uma chave de conta do BD Cosmos do Azure usando a CLI do Azure

Este exemplo regenera qualquer tipo de chave de conta do BD Cosmos do Azure usando a CLI do Azure. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/cosmosdb/secure-cosmosdb-regenerate-keys/secure-cosmosdb-regenerate-keys.sh?highlight=27-31 "Regenerar chaves de conta do BD Cosmos do Azure")]

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
| [az cosmosdb create](/cli/azure/cosmosdb/name#create) | Atualiza uma conta do BD Cosmos do Azure. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb/regenerate-key) | Regenera chaves de conta do BD Cosmos do Azure. |
| [az group delete](/cli/azure/resource#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do BD Cosmos do Azure podem ser encontrados na [Documentação da CLI do BD Cosmos do Azure](../cli-samples.md).

