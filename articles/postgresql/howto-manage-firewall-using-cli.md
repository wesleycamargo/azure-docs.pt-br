---
title: Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure | Microsoft Docs
description: Este artigo descreve como criar e gerenciar regras de firewall do banco de dados do Azure para PostgreSQL usando a linha de comando CLI do Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 01/18/2018
ms.openlocfilehash: 233479bff0a0d331161b52d33f31b45c5714362b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Criar e gerenciar regras de firewall do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure
As regras de firewall no nível de servidor permitem que os administradores gerenciem o acesso a um Banco de Dados SQL do Azure para servidor PostgreSQL de um endereço IP específico ou intervalo de endereços IP. Usando comandos convenientes da CLI do Azure, você pode criar, atualizar, excluir, listar e mostrar as regras de firewall para gerenciar o servidor. Para obter uma visão geral das regras de firewall do Banco de Dados do Azure para PostgreSQL, consulte [Regras de firewall do servidor de Banco de Dados do Azure para PostgreSQL](concepts-firewall-rules.md)

## <a name="prerequisites"></a>pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [banco de dados e servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md).
- Instalar o utilitário de linha de comando [CLI do Azure 2.0](/cli/azure/install-azure-cli) ou usar o Azure Cloud Shell no navegador.

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configurar regras de firewall para o Banco de Dados do Azure para PostgreSQL
Os comandos de [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) são usados para configurar regras de firewall.

## <a name="list-firewall-rules"></a>Listar regras de firewall 
Para listar as regras de firewall existentes no servidor, execute o comando [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_list).
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mypgserver-20170401
```
A saída listará as regras de firewall, se houver, no formato JSON por padrão. Você pode usar a opção `--output table` para obter um formato de tabela mais legível como saída.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Criar uma regra de firewall
Crie uma regra de firewall no nível de servidor do PostgreSQL do Azure com o comando [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). 

Ao especificar 0.0.0.0 como o `--start-ip-address` e 255.255.255.255 como o intervalo `--end-ip-address`, o exemplo a seguir permite que todos os endereços IP acessem o servidor **mypgserver-20170401.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mypgserver-20170401 --name AllowIpRange --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Para permitir o acesso a um endereço IP único, forneça tal endereço como `--start-ip-address` e `--end-ip-address`, como neste exemplo.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mypgserver-20170401 --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall que você criou, em formato JSON por padrão. Em caso de falha, a saída mostrará uma mensagem de erro.

## <a name="update-firewall-rule"></a>Atualizar uma regra de firewall 
Atualize uma regra de firewall existente no servidor usando o comando [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_update). Forneça o nome da regra de firewall existente como entrada, e os atributos IP inicial e IP final para atualizar.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mypgserver-20170401 --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall que você atualizou, em formato JSON por padrão. Em caso de falha, a saída mostrará uma mensagem de erro.
> [!NOTE]
> Se a regra de firewall não existir, ela será criada pelo comando de atualização.

## <a name="show-firewall-rule-details"></a>Mostrar detalhes da regra de firewall
Também é possível exibir os detalhes de uma regra de firewall existente no nível do servidor executando o comando [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_show).
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mypgserver-20170401 --name AllowIpRange
```
Após o êxito, a saída do comando listará os detalhes da regra do firewall que você especificou, em formato JSON por padrão. Em caso de falha, a saída mostrará uma mensagem de erro.

## <a name="delete-firewall-rule"></a>Excluir regra de firewall
Para revogar o acesso para um intervalo IP para o servidor, exclua uma regra de firewall existente executando o comando [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_delete). Forneça o nome da regra de firewall existente.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mypgserver-20170401 --name AllowIpRange
```
Após o êxito, não haverá saída. Em caso de falha, o texto da mensagem de erro retornará.

## <a name="next-steps"></a>Próximas etapas
- Da mesma forma, é possível usar um navegador da Web para [Criar e gerenciar as regras de firewall do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-manage-firewall-using-portal.md).
- Entenda mais sobre as [Regras de firewall do servidor de Banco de Dados do Azure para PostgreSQL](concepts-firewall-rules.md).
- Para obter ajuda com a conexão com um servidor do Banco de Dados do Azure para PostgreSQL, consulte [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md).
