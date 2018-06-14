---
title: Script do CLI do Azure – Baixar logs de servidor no Banco de Dados do Azure para PostgreSQL
description: Esse exemplo de script de CLI do Azure mostra como habilitar e fazer o download dos logs do servidor de um servidor de Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 1e62868523bece0d6154f0b4a32ea6231ecec1b3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
ms.locfileid: "29849555"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Habilitar e fazer o download dos logs de consulta lenta de um servidor de Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
Esse exemplo de script de CLI mostra como habilitar e fazer o download dos logs de consulta lenta de um único servidor de Banco de Dados do Azure para PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por executar a CLI localmente, este artigo exigirá a CLI do Azure versão 2.0 ou posterior. Verifique a versão executando `az --version`. Confira [Instalar a CLI do Azure 2.0]( /cli/azure/install-azure-cli) para instalar ou atualizar sua versão da CLI do Azure.

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios. Substitua <log_file_name> nos comandos `az monitor` pelo seu próprio nome de arquivo de log do servidor.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=18-19 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Limpar implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

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
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- [Configurar e acessar os logs de servidor no Portal do Azure](../howto-configure-server-logs-in-portal.md)
