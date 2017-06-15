---
title: 'CLI do Azure: Criar um banco de dados SQL | Microsoft Docs'
description: "Aprenda a criar um servidor lógico do Banco de Dados SQL, uma regra de firewall de nível de servidor e bancos de dados usando a CLI do Azure."
keywords: tutorial do banco de dados SQL, criar um banco de dados SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: dccebad561b04306126f986193a57aee176aaa0d
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Criar um único banco de dados SQL do Azure usando a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia detalha o uso da CLI do Azure para implantar um banco de dados SQL do Azure em um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este início rápido requer a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli-interactive
az login
```

## <a name="define-variables"></a>Definir variáveis

Defina as variáveis para usar nos scripts neste início rápido.

```azurecli-interactive
# The data center and resource name for your resources
export resourcegroupname = myResourceGroup
export location = westeurope
# The logical server name: Use a random value or replace with your own value (do not capitalize)
export servername = server-$RANDOM
# Set an admin login and password for your database
export adminlogin = ServerAdmin
export password = ChangeYourAdminPassword1
# The ip address range that you want to allow to access your DB
export startip = "0.0.0.0"
export endip = "0.0.0.1"
# The database name
export databasename = mySampleDatabase
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usando o comando [az group create](/cli/azure/group#create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

```azurecli-interactive
az group create --name $resourcegroupname --location $location
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md) usando o comando [az sql server create](/cli/azure/sql/server#create) . Um servidor lógico contém um grupo de bancos de dados gerenciados conjuntamente. O exemplo a seguir cria um servidor nomeado aleatoriamente no seu grupo de recursos com logon de administrador `ServerAdmin` e senha `ChangeYourAdminPassword1`. Substitua esses valores predefinidos como desejado.

```azurecli-interactive
az sql server create --name $servername --resource-group $resourcegroupname --location $location \
    --admin-user $adminlogin --admin-password $password
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma [ regra de firewall no nível de servidor de banco de dados SQL do Azure](sql-database-firewall-configure.md) usando o comando [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create). Uma regra de firewall no nível de servidor permite que um aplicativo externo, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte ao banco de dados SQL através do firewall do serviço de Banco de Dados SQL. No exemplo a seguir, o firewall está aberto somente para os outros recursos do Azure. Para habilitar a conectividade externa, altere o endereço IP para um endereço apropriado para seu ambiente. Para abrir todos os endereços IP, use 0.0.0.0 como o endereço IP inicial e 255.255.255.255 como o endereço final.  

```azurecli-interactive
az sql server firewall-rule create --resource-group $resourcegroupname --server $servername \
    -n AllowYourIp --start-ip-address $startip --end-ip-address $endip
```

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não conseguirá conectar seu servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Criar um banco de dados no servidor com dados de exemplo

Crie um banco de dados com [nível de desempenho S0](sql-database-service-tiers.md) no servidor usando o comando [az sql db create](/cli/azure/sql/db#create). O exemplo a seguir cria um banco de dados denominado `mySampleDatabase` e carrega os dados de exemplo AdventureWorksLT nesse banco de dados. Substitua os valores predefinidos conforme desejado (outros inícios rápidos nesta coleção aproveitam os valores neste início rápido).

```azurecli-interactive
az sql db create --resource-group $resourcegroupname --server $servername \
    --name $databasename --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos nessa coleção aproveitam esse início rápido. 

> [!TIP]
> Se você planeja continuar trabalhando com os inícios rápidos subsequentes, não limpe os recursos criados nesse início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.
>

```azurecli-interactive
az group delete --name $resourcegroupname
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um banco de dados, você pode se conectar e consultar usando suas ferramentas favoritas. Saiba mais escolhendo sua ferramenta abaixo:

- [SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [Visual Studio Code](sql-database-connect-query-vscode.md)
- [.NET](sql-database-connect-query-dotnet.md)
- [PHP](sql-database-connect-query-php.md)
- [Node.js](sql-database-connect-query-nodejs.md)
- [Java](sql-database-connect-query-java.md)
- [Python](sql-database-connect-query-python.md)
- [Ruby](sql-database-connect-query-ruby.md)


