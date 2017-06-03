---
title: "Script da CLI do Azure - Criar uma conta, banco de dados e coleção da API de DocumentDB de Banco de Dados Cosmo do Azure | Microsoft Docs"
description: "Exemplo de script da CLI do Azure - Criar uma conta, banco de dados e coleção da API de DocumentDB de Banco de Dados Cosmo do Azure"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: bad18882dd362ded4dac53beecbd65ff6df4c725
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017

---

# <a name="azure-cosmos-db-create-an-documentdb-api-account-using-cli"></a>Banco de Dados Cosmos do Azure: Criar uma conta da API do DocumentDB usando a CLI

Este exemplo de script da CLI cria uma conta, banco de dados e coleção da API de DocumentDB de Banco de Dados Cosmo do Azure.  

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-account-database/create-cosmosdb-account-database.sh?highlight=15-35 "Criar uma conta, banco de dados e coleção da API de DocumentDB de Banco de Dados Cosmo do Azure")]

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
| [az cosmosdb create](/cli/azure/cosmosdb#create) | Cria uma conta do Banco de Dados Cosmos do Azure. |
| [az group delete](/cli/azure/resource#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Banco de Dados Cosmos do Azure podem ser encontrados na [Documentação da CLI do Banco de Dados Cosmos do Azure](../cli-samples.md).

