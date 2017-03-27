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
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/14/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 01e0c45556b67b844a4e4ab5e0eb8644e8673dd1
ms.lasthandoff: 03/10/2017

---

# <a name="scale-an-elastic-pool-in-azure-sql-database-using-the-azure-cli"></a>Dimensionar um pool elástico no Banco de Dados SQL do Azure usando a CLI do Azure

Esse script da CLI de exemplo cria pools elásticos, move os bancos de dados em pools e altera os níveis de desempenho. 

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`. 

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no Windows, veja [Execução da CLI do Azure no Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Mover banco de dados entre pools")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, um servidor lógico, um Banco de Dados SQL e as regras de firewall. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
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

