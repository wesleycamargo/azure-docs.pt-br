---
title: "Configurar os parâmetros de serviço no Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Descreve como configurar os parâmetros de serviço no Banco de Dados do Azure para PostgreSQL."
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
ms.openlocfilehash: 7e46e3d588782ec0f2ef89c58001e997b8fc2910
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personalizar os parâmetros de configuração do servidor usando a CLI do Azure
Você pode listar, exibir e atualizar os parâmetros de configuração de um servidor PostgreSQL do Azure usando a CLI (Interface de linha de comando) do Azure. No entanto, somente um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um servidor e um banco de dados [Criar um Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md)
- O utilitário de linha de comando [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) instalado

## <a name="list-server-configuration-parameters-for-azure-postgresql-server"></a>Listar os parâmetros de configuração de servidor para o servidor PostgreSQL do Azure
Para obter uma lista de todos os parâmetros modificáveis em um servidor PostgreSQL do Azure e seus valores, execute o seguinte comando **az postgres server configuration** como mostrado aqui:
> az postgres server configuration list --resource-group <resource group name> --server <server name>

Por exemplo, você pode listar os parâmetros de configuração de servidor para o servidor PostgreSQL do Azure **mypgserver.postgres.database.azure.com** no Grupo de Recursos **myresourcegroup**.
```azurecli
az postgres server configuration list --resource-group myresourcegroup --server mypgserver
```
## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar os detalhes sobre um parâmetro de configuração específico para um servidor PostgreSQL do Azure, execute o comando **az postgres server configuration**.
```azurecli
az postgres server configuration show --name <configuration name>
   --resource-group <resource group name> --server <server name>
```
Por exemplo, você pode mostrar detalhes do parâmetro de configuração de servidor **log\_min\_messages** para o servidor PostgreSQL do Azure **mypgserver.postgres.database.azure.com** no Grupo de Recursos **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages --resource-group myresourcegroup --server mypgserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modificar o valor do parâmetro de configuração do servidor
Você pode modificar o valor de determinados parâmetros de configuração de um servidor PostgreSQL do Azure, e isso atualizará o valor da configuração subjacente para o mecanismo do servidor PostgreSQL. Para atualizar o valor de configuração, execute o seguinte comando **az postgres server configuration**. 
```azurecli
az postgres server configuration update --name <configuration name>
   --resource-group <resource group name> --server <server name>[--value]
```
Por exemplo, você pode atualizar o parâmetro de configuração de servidor **log\_min\_messages** do servidor PostgreSQL do Azure **mypgserver.postgres.database.azure.com** no Grupo de Recursos **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages 
   --resource-group myresourcegroup --server mypgserver --value INFO
```
Se você quiser redefinir o valor de um parâmetro de configuração, basta simplesmente escolher de fora o parâmetro opcional -value e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:
```azurecli
az postgres server configuration show --name log\_min\_messages
   --resource-group myresourcegroup --server mypgserver
```
Isso redefinirá a configuração log\_min\_messages para o valor padrão WARNING. Para obter mais detalhes sobre a configuração do servidor e os valores permitidos, veja a documentação do PostgreSQL em [Configuração do Servidor](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Próximas etapas
- Para configurar e acessar os logs de servidor, confira [Logs de servidor no Banco de Dados do Azure para PostgreSQL](concepts-server-logs.md)
