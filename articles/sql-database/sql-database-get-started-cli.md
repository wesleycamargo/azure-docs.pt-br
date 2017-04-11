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
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 04/04/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 7933567f6c10262b14ea1656b26d8fac148942ea
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Criar um único banco de dados SQL do Azure usando a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia detalha o uso da CLI do Azure para implantar um banco de dados SQL do Azure em um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md).

Para concluir este início rápido, certifique-se de que a versão mais recente da [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) esteja instalada. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) com o comando [az group create](/cli/azure/group#create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados em grupo. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md) com o comando [az sql server create](/cli/azure/sql/server#create) . Um servidor lógico contém um grupo de bancos de dados gerenciados conjuntamente. O exemplo a seguir cria um servidor nomeado aleatoriamente no seu grupo de recursos com logon de administrador `ServerAdmin` e senha `ChangeYourAdminPassword1`. Substitua esses valores predefinidos como desejado.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma [ regra de firewall no nível de servidor de banco de dados SQL do Azure](sql-database-firewall-configure.md) com o comando [az sql server firewall create](/cli/azure/sql/server/firewall#create). Uma regra de firewall no nível de servidor permite que um aplicativo externo, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte ao banco de dados SQL através do firewall do serviço de Banco de Dados SQL. O exemplo a seguir cria uma regra de firewall para um intervalo de endereços predefinidos, que, neste exemplo, é intervalo inteiro possível de endereços IP. Substitua esses valores predefinidos pelos valores do endereço IP externo ou do intervalo de endereços IP. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server-with-sample-data"></a>Criar um banco de dados no servidor com dados de exemplo

Crie um banco de dados com [nível de desempenho S0](sql-database-service-tiers.md) no servidor com o comando [az sql db create](/cli/azure/sql/db#create). O exemplo a seguir cria um banco de dados denominado `mySampleDatabase` e carrega os dados de exemplo AdventureWorksLT nesse banco de dados. Substitua os valores predefinidos conforme desejado (outros inícios rápidos nesta coleção aproveitam os valores neste início rápido).

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos nessa coleção aproveitam esse início rápido. Se você planeja continuar trabalhando com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados nesse início rápido. Caso contrário, use os comandos a seguir para excluir todos os recursos criados por esse início rápido.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para se conectar usando o Visual Studio, veja [Conectar e consultar com o Visual Studio](sql-database-connect-query.md).
- Para obter uma visão geral técnica do banco de dados SQL, veja [Sobre o serviço Banco de Dados SQL](sql-database-technical-overview.md).

