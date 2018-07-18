---
title: Exemplo da CLI para criar um banco de dados SQL do Azure | Microsoft Docs
description: Use este script de exemplo da CLI do Azure para criar um banco de dados SQL.
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: janeng
editor: carlrab
ms.service: sql-database
ms.custom: DBs & servers, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.author: carlrab
ms.date: 05/17/2018
ms.openlocfilehash: bca779e55d14a6ff3cb6da0984de93123cf23903
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365722"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Use a CLI para criar um banco de dados SQL do Azure individual e configurar uma regra de firewall

Este exemplo de script de CLI do Azure cria um banco de dados SQL do Azure e configura uma regra de firewall no nível do servidor. Depois que o script tiver sido executado com êxito, o Banco de Dados SQL poderá ser acessado de todos os serviços do Azure e o endereço IP configurado. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#az_sql_server_create) | Cria um servidor lógico que hospeda o Banco de Dados SQL. |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | Cria uma regra de firewall para permitir o acesso a todos os bancos de dados SQL no servidor do intervalo de endereços IP inserido. |
| [az sql db create](/cli/azure/sql/db#az_sql_db_create) | Cria o Banco de Dados SQL no servidor lógico. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../sql-database-cli-samples.md).

