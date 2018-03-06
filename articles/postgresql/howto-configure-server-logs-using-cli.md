---
title: Configurar e acessar logs do servidor para PostgreSQL usando CLI do Azure
description: "Este artigo descreve como configurar e acessar os registros de serviço no bando de dados do Azure para PostgreSQL usando a linha de comando CLI do Azure."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e12a8907b641b4591ed5ff9fdd5d8458eb75525e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configurar e acessar logs de servidor usando a CLI do Azure
Você pode listar e baixar logs de erro do servidor PostgreSQL do Azure usando a interface de linha de comando (Azure CLI). No entanto, não há suporte para acesso aos logs de transação. 

## <a name="prerequisites"></a>pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instalar o utilitário de linha de comando [CLI do Azure 2.0](/cli/azure/install-azure-cli) ou usar o Azure Cloud Shell no navegador.

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configurar o registro em log para o Banco de Dados do Azure para PostgreSQL
Você pode configurar o servidor para acessar os logs de erro e os logs de consulta. Os logs de erros podem conter informações de pontos de verificação, conexão e vácuo automático.
1. Ativar o registro em log
2. Atualizar log\_statement e log\_min\_duration\_statement para habilitar o registro em log de consulta
3. Atualizar o período de retenção

Para mais informações, confira [personalizando os parâmetros de configuração do servidor](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Liste os registros para servidor de Banco de Dados do Azure para PostgreSQL
Para listar os arquivos de log disponíveis para o servidor, execute o comando [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list).

Você pode listar os arquivos de log para o servidor **mydemoserver.postgres.database.azure.com** no Grupo de Recursos **myresourcegroup** e direcioná-los para um arquivo de texto chamado **log\_files\_list.txt.**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Baixa logs localmente do servidor
O comando [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) permite que você baixe arquivos de log individuais para o seu servidor. 

Esse exemplo baixa o arquivo de log específico para o servidor **mydemoserver.postgres.database.azure.com** no Grupo de Recursos **myresourcegroup** para seu ambiente local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre os logs de servidor, confira [Logs de servidor no Banco de Dados do Azure para PostgreSQL](concepts-server-logs.md)
- Para saber mais sobre os parâmetros de servidor, veja [Personalizar os parâmetros de configuração de servidor usando a CLI do Azure](howto-configure-server-parameters-using-cli.md)
