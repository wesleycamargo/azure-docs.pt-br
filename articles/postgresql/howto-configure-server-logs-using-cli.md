---
title: Configurar e acessar os logs do servidor para PostgreSQL usando a CLI do Azure | Microsoft Docs
description: Descreve como configurar e acessar os logs de servidor no Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: dc047e6d5194035586931b3cacc4d5cd07f6cfc2
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configurar e acessar logs de servidor usando a CLI do Azure
Você pode listar e baixar logs de erro do servidor PostgreSQL do Azure usando a CLI (Interface de linha de comando) do Azure. No entanto, não há suporte para acesso aos logs de transação. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [servidor de Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- O utilitário de linha de comando [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) instalado

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configurar o registro em log para o Banco de Dados do Azure para PostgreSQL
Você pode configurar o servidor para acessar os logs de erro e os logs de consulta. Os logs de erros podem conter informações de pontos de verificação, conexão e vácuo automática.
1. Ativar o registro em log
2. Atualizar log\_statement e log\_min\_duration\_statement para habilitar o registro em log de consulta
3. Atualizar o período de retenção

Confira [personalizar os parâmetros de configuração do servidor](howto-configure-server-parameters-using-cli.md) para saber mais.

## <a name="list-logs-for-azure-postgresql-server"></a>Listar logs para o servidor PostgreSQL do Azure
Para listar os arquivos de log disponíveis para o servidor, execute o comando **az postgres server-logs** conforme mostra o exemplo a seguir:
```azurecli
az postgres server-logs list --resource-group <resource group name> --server <server name> [ --file-last-written --filename-contains --max-file-size ]
```
Por exemplo, você pode listar os arquivos de log do servidor PostgreSQL do Azure **mypgserver.postgres.database.azure.com** no Grupo de Recursos **myresourcegroup** e direcioná-lo para um arquivo de texto chamado **log\_files\_list.txt. **
```azurecli
az postgres server-logs list --resource-group **myresourcegroup** --server **mypgserver** > log\_files\_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Baixa logs localmente do servidor
Você também pode baixar os arquivos de log individuais para seu servidor PostgreSQL do Azure. 
```azurecli
az postgres server-logs download –name <log file name> --resource-group <resource group name> --server <server name>
```
Este exemplo baixa o arquivo de log específico para o servidor PostgreSQL do Azure **mypgserver.postgres.database.azure.com** no Grupo de Recursos **myresourcegroup** para seu ambiente local.
```azurecli
az postgres server-logs download --name 20170414-mypgserver-postgresql.log --resource-group **myresourcegroup** --server **mypgserver**
```
## <a name="next-steps"></a>Próximas etapas
- Para saber mais sobre os logs de servidor, confira [Logs de servidor no Banco de Dados do Azure para PostgreSQL](concepts-server-logs.md)
- Para saber mais sobre os parâmetros de servidor, veja [Personalizar os parâmetros de configuração de servidor usando a CLI do Azure](howto-configure-server-parameters-using-cli.md)
