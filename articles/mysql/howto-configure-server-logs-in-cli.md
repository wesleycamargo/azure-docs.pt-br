---
title: Logs do servidor de acesso no Banco de Dados do Azure para MySQL usando a CLI do Azure
description: Este artigo descreve como acessar os logs de servidor no Banco de Dados do Azure para MySQL usando o utilitário de linha de comando da CLI do Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 57b72ded77484dc1c8ca4c62811b62e171365db4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423450"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurar e acessar logs de servidor usando a CLI do Azure
É possível baixar os logs de servidor do Banco de Dados do Azure para MySQL usando a CLI do Azure, o utilitário de linha de comando do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- [Servidor do Banco de Dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- A [CLI do Azure 2.0](/cli/azure/install-azure-cli) ou o Azure Cloud Shell no navegador

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configurar o registro em log para o Banco de Dados do Azure para MySQL
É possível configurar o servidor para acessar os logs de consulta lenta do MySQL executando as seguintes etapas:
1. Ative o registro em log definindo o parâmetro **slow\_query\_log** como ON.
2. Ajuste outros parâmetros como **long\_query\_time** e **log\_slow\_admin\_statements**.

Para saber como definir o valor desses parâmetros por meio da CLI do Azure, consulte [Como configurar parâmetros do servidor](howto-configure-server-parameters-using-cli.md). 

Por exemplo, o comando da CLI a seguir ativa o log de consultas lentas, define o tempo de consulta longo para 10 segundos e desativa o registro em log da instrução de admin lenta. Por fim, ele lista as opções de configuração para sua análise.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Listar os logs para o servidor do Banco de Dados do Azure para MySQL
Para listar os arquivos de log disponíveis para o servidor, execute o comando [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list).

Você pode listar os arquivos de log para o servidor **mydemoserver.mysql.database.azure.com** no Grupo de Recursos **myresourcegroup**. Em seguida, direcione-os para um arquivo de texto chamado **log\_files\_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Baixar logs do servidor
Com o comando [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download), você pode baixar arquivos de log individuais para o seu servidor. 

Use o exemplo a seguir para baixar o arquivo de log específico para o servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup** para seu ambiente local.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Logs de servidor no Banco de Dados do Azure para MySQL](concepts-server-logs.md).
