---
title: "Script da CLI do Azure – Restaurar um servidor do Banco de Dados do Azure para PostgreSQL"
description: Esse script de exemplo de CLI do Azure mostra como restaurar um servidor de Banco de Dados do Azure para PostgreSQL e seus bancos de dados a um ponto anterior.
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
ms.openlocfilehash: 14e365d90942a09db8bbd2f3eeb4c72a8be3819b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Restaurar um servidor do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
Esse script de exemplo do CLI restaura um servidor individual do Banco de Dados do Azure para PostgreSQL a um ponto anterior.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por executar a CLI localmente, este artigo exigirá a CLI do Azure versão 2.0 ou posterior. Verifique a versão executando `az --version`. Confira [Instalar a CLI do Azure 2.0]( /cli/azure/install-azure-cli) para instalar ou atualizar sua versão da CLI do Azure.

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios. Substitua a ID de assinatura usada nos comandos `az monitor` pela sua ID de assinatura.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=18-19 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpar implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgresql server create](/cli/azure/postgresql/server#az_msql_server_create) | Cria um servidor PostgreSQL que hospeda os bancos de dados. |
| [az postgresql server restore](/cli/azure/postgresql/server#az_msql_server_restore) | Restaura um servidor de backup. |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o portal do Azure](../howto-restore-server-portal.md)
