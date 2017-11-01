---
title: Logs do servidor de acesso no Banco de Dados do Azure para MySQL usando a CLI do Azure | Microsoft Docs
description: "Este artigo descreve como acessar os logs de servidor no Banco de Dados do Azure para MySQL usando o utilitário de linha de comando da CLI do Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/18/2017
ms.openlocfilehash: 6ee2c2c6e6cff824d3167ea600a1ddc778ad011b
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configurar e acessar logs de servidor usando a CLI do Azure
É possível baixar os logs de servidor do Banco de Dados do Azure para MySQL usando a CLI do Azure, o utilitário de linha de comando do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Servidor do Banco de Dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- A [CLI do Azure 2.0](/cli/azure/install-azure-cli) ou use o Azure Cloud Shell no navegador.

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configurar o registro em log para o Banco de Dados do Azure para MySQL
É possível configurar o servidor para acessar os logs de consulta lenta do MySQL.
1. Ative o registro em log definindo o parâmetro **slow\_query\_log** como ON.
2. Ajuste outros parâmetros como **long\_query\_time** e **log\_slow\_admin\_statements**.

Consulte [Como configurar parâmetros do servidor](howto-configure-server-parameters-using-cli.md) para saber como definir o valor desses parâmetros por meio da CLI do Azure.

Por exemplo, o comando da CLI a seguir ativa o log de consultas lentas, define o tempo de consulta longo para 10 segundos e desativa o registro em log da instrução de admin lenta. Por fim, ele lista as opções de configuração para sua análise.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Listar os logs para o servidor do Banco de Dados do Azure para MySQL
Para listar os arquivos de log disponíveis para o servidor, execute o comando [az mysql server-logs list](/cli/azure/mysql/server-logs#list).

Você pode listar os arquivos de log para o servidor **myserver4demo.mysql.database.azure.com** no Grupo de Recursos **myresourcegroup** e direcioná-los para um arquivo de texto chamado **log\_files\_list.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Baixar logs do servidor
O comando [az mysql server-logs download](/cli/azure/mysql/server-logs#download) permite que você baixe arquivos de log individuais para o seu servidor. 

Este exemplo baixa o arquivo de log específico para o servidor **myserver4demo.mysql.database.azure.com** no Grupo de Recursos **myresourcegroup** para seu ambiente local.
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Logs de servidor no Banco de Dados do Azure para MySQL](concepts-server-logs.md)
