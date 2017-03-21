---
title: "CLI do Azure: Criar e consultar um único banco de dados SQL | Microsoft Docs"
description: "Aprenda a criar um servidor lógico do Banco de Dados SQL, uma regra de firewall de nível de servidor e bancos de dados usando a CLI do Azure."
keywords: tutorial do banco de dados SQL, criar um banco de dados SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: cli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8322e46f462b6c940f9808411d99aa1cee0beea5
ms.lasthandoff: 03/15/2017

---

# <a name="create-and-query-a-single-azure-sql-database-with-the-azure-cli"></a>Criar um único banco de dados SQL do Azure com a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Esses guia detalha o uso da CLI do Azure para implantar um banco de dados SQL do Azure.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para saber mais, veja o [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e acompanhe as instruções na tela.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Criar um servidor lógico

Crie um servidor lógico com o comando [az sql server create](/cli/azure/sql/server#create). O exemplo a seguir cria um servidor nomeado aleatoriamente em seu grupo de recursos com um logon de administrador chamado `ServerAdmin` e uma senha `ChangeYourAdminPassword1`. Substitua esses valores predefinidos como desejado.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma regra de firewall no nível de servidor com o comando [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create). Uma regra de firewall no nível de servidor permite que um aplicativo externo, como o SQL Server Management Studio ou o utilitário SQLCMD, se conecte ao banco de dados SQL através do firewall do serviço de Banco de Dados SQL. O exemplo a seguir cria uma regra de firewall para um intervalo de endereços predefinidos, que, neste exemplo, é intervalo inteiro possível de endereços IP. Substitua esses valores predefinidos pelos valores do endereço IP externo ou do intervalo de endereços IP. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server"></a>Criar um banco de dados no servidor

Crie um banco de dados no servidor com o comando [az sql db create](/cli/azure/sql/db#create). O exemplo a seguir cria um banco de dados vazio chamado `mySampleDatabase`. Substitua esse valor predefinido conforme desejado.

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --service-objective S0
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos criados por este Início Rápido, execute o seguinte comando:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para se conectar usando o Visual Studio, veja [Conectar e consultar com o Visual Studio](sql-database-connect-query.md).
- Para obter uma visão geral técnica do banco de dados SQL, veja [Sobre o serviço Banco de Dados SQL](sql-database-technical-overview.md).

