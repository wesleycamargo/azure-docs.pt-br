---
title: Azure CLI Script-criar um Banco de Dados SQL | Microsoft Docs
description: "Amostra de script da CLI do Azure – Criar um Banco de Dados SQL usando a CLI do Azure"
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
ms.openlocfilehash: 4a7343ebc5090dc82f6c7b470c697167536315ca
ms.lasthandoff: 03/10/2017

---

# <a name="create-a-single-sql-database-and-configure-a-firewall-rule-using-the-azure-cli"></a>Criar um Banco de Dados SQL e configurar uma regra de firewall usando a CLI do Azure

Este script de CLI de exemplo cria um Banco de Dados SQL do Azure e configura uma regra de firewall no nível do servidor. Depois que o script tiver sido executado com êxito, o Banco de Dados SQL poderá ser acessado de todos os serviços do Azure e o endereço IP configurado. 

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o comando `az login`. 

Este exemplo funciona em um shell Bash. Para opções sobre como executar scripts da CLI do Azure no Windows, veja [Execução da CLI do Azure no Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Criar Banco de Dados SQL")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#create) | Cria um servidor lógico que hospeda o Banco de Dados SQL. |
| [az sql server firewall create](/cli/azure/sql/server/firewall#create) | Cria uma regra de firewall para permitir o acesso a todos os bancos de dados SQL no servidor do intervalo de endereços IP inserido. |
| [az sql db create](/cli/azure/sql/db#create) | Cria o Banco de Dados SQL no servidor lógico. |
| [az group delete](/cli/azure/resource#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../sql-database-cli-samples.md).


