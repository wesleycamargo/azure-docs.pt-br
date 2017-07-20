---
title: "Configurar os parâmetros de serviço no Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Este artigo descreve como configurar os parâmetros de serviço no bando de dados do Azure para PostgreSQL usando a linha de comando CLI do Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 9a575148a05843bef7524eff61407b377292ca3b
ms.contentlocale: pt-br
ms.lasthandoff: 06/16/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personalizar os parâmetros de configuração do servidor usando a CLI do Azure
Você pode listar, exibir e atualizar os parâmetros de configuração de um servidor PostgreSQL do Azure usando a CLI (Interface de linha de comando) do Azure. No entanto, somente um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um servidor e um banco de dados [Criar um Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Instalar o utilitário de linha de comando [CLI do Azure 2.0](/cli/azure/install-azure-cli) ou usar o Azure Cloud Shell no navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Listar os parâmetros de configuração de servidor para o bando de dados do Azure para servidor PostgreSQL
Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [az postgres server configuration list](/cli/azure/postgres/server/configuration#list).

Você pode listar os parâmetros de configuração de servidor **mypgserver-20170401.postgres.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar os detalhes sobre um parâmetro de configuração específico para um servidor, execute o comando [az postgres server configuration show](/cli/azure/postgres/server/configuration#show).

Este exemplo mostra detalhes do **registro\_min\_mensagens** parâmetros de configuração de servidor para servidor **mypgserver-20170401.postgres.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="modify-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor
Você pode modificar o valor de determinados parâmetros de configuração e isso atualizará o valor da configuração subjacente para o mecanismo do servidor PostgreSQL. Para atualizar o valor de configuração execute o comando [az postgres server configuration set](/cli/azure/postgres/server/configuration#set). 

Para atualizar o **registro\_min\_mensagens** parâmetros de configuração de servidor para servidor **mypgserver-20170401.postgres.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401 --value INFO
```
Se você quiser redefinir o valor de um parâmetro de configuração, basta simplesmente escolher deixar o parâmetro opcional `--value` e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
Isso redefinirá o **registro\_min\_mensagens** configuração para o valor padrão **AVISO**. Para obter mais detalhes sobre a configuração do servidor e os valores permitidos, veja a documentação do PostgreSQL em [Configuração do Servidor](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Próximas etapas
- Para configurar e acessar os logs de servidor, confira [Logs de servidor no Banco de Dados do Azure para PostgreSQL](concepts-server-logs.md)

