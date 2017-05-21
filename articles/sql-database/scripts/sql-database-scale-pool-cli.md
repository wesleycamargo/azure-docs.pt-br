---
title: "Script da CLI do Azure dimensiona um pool elástico | Microsoft Docs"
description: "Exemplo de script da CLI do Azure - Dimensionar um pool de banco de dados elástico"
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
ms.topic: samples
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/24/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 5ca7befc4b7ea031287b81ae6834496579b480fd
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017

---

# <a name="scale-an-elastic-pool-in-azure-sql-database-using-the-azure-cli"></a>Dimensionar um pool elástico no Banco de Dados SQL do Azure usando a CLI do Azure

Esse script da CLI de exemplo cria pools elásticos, move os bancos de dados em pools e altera os níveis de desempenho. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[principal](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Mover banco de dados entre pools")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, um servidor lógico, um Banco de Dados SQL e as regras de firewall. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Cria um servidor lógico que hospeda o Banco de Dados SQL. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | Cria um pool de banco de dados elástico dentro do servidor lógico. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Cria o Banco de Dados SQL no servidor lógico. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pools#update) | Atualiza um pool de banco de dados elástico, neste exemplo altera o eDTU atribuído. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../sql-database-cli-samples.md).

