---
title: Configurar os parâmetros de serviço no Banco de Dados do Azure para MySQL
description: Este artigo descreve como configurar os parâmetros de serviço no Banco de Dados do Azure para MySQL usando o utilitário da linha de comando da CLI do Azure.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 4c04cb77513ec070edce739aa0a49447dc915a1b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265207"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personalizar os parâmetros de configuração do servidor usando a CLI do Azure
É possível listar, exibir e atualizar os parâmetros de configuração de um servidor de Banco de Dados do Azure para MySQL usando o utilitário da linha de comando da CLI do Azure. Um subconjunto de configurações de mecanismo é exposto no nível do servidor e pode ser modificado. 

## <a name="prerequisites"></a>pré-requisitos
Para seguir este guia de instruções, você precisa:
- [um servidor de Banco de Dados do Azure para MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- o utilitário de linha de comando da [CLI do Azure 2.0](/cli/azure/install-azure-cli) ou usar o Azure Cloud Shell no navegador.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Listar os parâmetros de configuração de servidor para o Banco de Dados do Azure para MySQL
Para listar todos os parâmetros modificáveis em um servidor e seus valores, execute o comando [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list).

É possível listar os parâmetros de configuração do servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Para obter a definição de cada um dos parâmetros listados, consulte a seção de referência do MySQL em [Variáveis do Sistema do Servidor](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Mostrar detalhes do parâmetro de configuração do servidor
Para mostrar os detalhes sobre um parâmetro de configuração específico de um servidor, execute o comando [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show).

Este exemplo mostra detalhes do parâmetro de configuração de servidor **slow\_query\_log** para o servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modificar um valor do parâmetro de configuração do servidor
Você também pode modificar o valor de determinados parâmetros de configuração, que atualiza o valor da configuração subjacente para o mecanismo do servidor MySQL. Para atualizar o valor de configuração execute o comando [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set). 

Para atualizar o parâmetro de configuração de servidor **slow\_query\_log** do servidor **mydemoserver.mysql.database.azure.com** no grupo de recursos **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Se você quiser redefinir o valor de um parâmetro de configuração, omita o parâmetro opcional `--value` e o serviço aplicará o valor padrão. No exemplo acima, ele teria a seguinte aparência:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Esse código redefine a configuração **log de\_consultas\_lentas** para o valor padrão **OFF**. 

## <a name="next-steps"></a>Próximas etapas
- Como configurar [parâmetros de servidor no Portal do Azure](howto-server-parameters.md)