---
title: "Script da CLI do Azure: Alterar as configurações do servidor"
description: "Este script de exemplo da CLI lista todas as configurações do servidor disponíveis e atualiza o valor de uma das opções."
services: postgres
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 334322557c83d5576c9a11def6bb0dc0e3ad1fec
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2018
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Listar e atualizar as configurações de um servidor do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
Este script de exemplo da CLI lista todos os parâmetros de configuração disponíveis, bem como seus valores permitidos para o servidor do Banco de Dados do Azure para PostgreSQL e define o *log_retention_days* como um valor diferente do padrão.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, altere as linhas destacadas para personalizar o nome de usuário administrador e a senha.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpar implantação
Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Cria um servidor PostgreSQL que hospeda os bancos de dados. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Liste as configurações de um servidor do Banco de Dados do Azure para PostgreSQL. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Atualize as configurações de um servidor do Banco de Dados do Azure para PostgreSQL. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) | Mostre as configurações de um servidor do Banco de Dados do Azure para PostgreSQL. |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure/overview).
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- Para saber mais sobre parâmetros do servidor, veja [Como configurar parâmetros do servidor no portal do Azure](../howto-configure-server-parameters-using-portal.md).
