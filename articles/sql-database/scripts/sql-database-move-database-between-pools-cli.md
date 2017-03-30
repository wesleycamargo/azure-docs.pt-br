---
title: "Azure CLI Script-mover um Banco de Dados SQL e pools Elásticos | Microsoft Docs"
description: "Amostra de Script CLI do Azure – Mover um Banco de Dados SQL entre pools elásticos usando a CLI do Azure"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/16/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 15d8f075a21c335de862dc004fc4e6a47d8bc38b
ms.lasthandoff: 03/30/2017

---

# <a name="create-elastic-pools-and-move-databases-between-pools-and-out-of-a-pool-using-the-azure-cli"></a>Criar pools Elásticos e mover bancos de dados entre pools e fora de um pool usando a CLI do Azure

Este exemplo de script CLI cria dois pools elásticos e move um banco de dados de um pool elástico para outro pool elástico, depois move um banco de dados fora de um pool elástico para um nível de desempenho de banco de dados individual. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Mover banco de dados entre pools")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Cria um servidor lógico que hospeda um banco de dados ou pool elástico. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | Cria um pool elástico dentro do servidor lógico. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Cria um banco de dados em um servidor lógico como um banco de dados individual ou em pool. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | Atualiza as propriedades do banco de dados ou move um banco de dados para dentro, para fora entre pools elásticos. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../sql-database-cli-samples.md).



