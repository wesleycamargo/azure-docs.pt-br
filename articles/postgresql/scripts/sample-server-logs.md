---
title: 'Script do CLI do Azure: fazer o download de logs de servidor no Banco de Dados do Azure para PostgreSQL'
description: Esse exemplo de script de CLI do Azure mostra como habilitar e fazer o download dos logs do servidor de um servidor de Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 27dad83546c5f6d5bac18d61a1e223ced1158236
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Habilitar e fazer o download dos logs de consulta lenta de um servidor de Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
Esse exemplo de script de CLI mostra como habilitar e fazer o download dos logs de consulta lenta de um único servidor de Banco de Dados do Azure para PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este exemplo exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, altere as linhas destacadas para personalizar o nome de usuário administrador e a senha. Substitua <log_file_name> nos comandos de monitor az com seu próprio nome de arquivo de log do servidor.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Limpar implantação
Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server#az_msql_server_create) | Cria um servidor PostgreSQL que hospeda os bancos de dados. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Liste os valores de configuração para um servidor. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Atualize a configuração de um servidor. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | Lista arquivos de log para um servidor. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | Faça o download de arquivos de log. |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure/overview).
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- [Configurar e acessar os logs de servidor no Portal do Azure](../howto-configure-server-logs-in-portal.md)
