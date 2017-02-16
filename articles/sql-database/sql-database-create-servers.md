---
title: Criar servidores de Banco de Dados SQL do Azure | Microsoft Docs
description: "Referência rápida sobre como criar servidores de Banco de Dados SQL do Azure usando o Portal do Azure e o PowerShell."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: e8fe805da2446895b6616926fe1326dc30b9d0d0


---

# <a name="create-azure-sql-database-servers"></a>Criar servidores de Banco de Dados SQL do Azure

Você pode criar um servidor de Banco de Dados SQL do Azure usando o [Portal do Azure](https://portal.azure.com/), o PowerShell, a API REST ou a C#. 

## <a name="create-an-azure-sql-database-server-using-the-azure-portal"></a>Criar um servidor de Banco de Dados SQL do Azure usando o Portal do Azure

1. Abra a folha **Servidores SQL** no [Portal do Azure](https://portal.azure.com/). 

    ![servidores sql](./media/sql-database-get-started/new-sql-server.png)

2. Clique em **Adicionar** para criar um SQL Server

    ![adicionar novo SQL Server](./media/sql-database-get-started/new-sql-server-add.png)

> [!TIP]
> Para ver um tutorial de introdução ao uso do Portal do Azure e ao uso do SQL Server Management Studio, veja [Introdução aos servidores do Banco de Dados SQL do Azure, aos bancos de dados e às regras de firewall usando o Portal do Azure e o SQL Server Management Studio](sql-database-get-started.md).
>

## <a name="create-an-azure-sql-database-server-using-powershell"></a>Criar um servidor de Banco de Dados SQL do Azure usando o PowerShell

Para criar um servidor de Banco de Dados SQL, use o cmdlet [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver). Substitua *server1* pelo nome do seu servidor. Os nomes dos servidores devem ser exclusivos para todos os servidores do Banco de Dados SQL do Azure. Se o nome do servidor já existir, você obterá um erro. Esse comando pode levar vários minutos para ser concluído. O grupo de recursos já precisa existir na sua assinatura.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

> [!TIP]
> Para obter um script de exemplo, veja [Criar um script do PowerShell do banco de dados SQL](sql-database-get-started-powershell.md).
>

## <a name="additional-resources"></a>Recursos adicionais
* Para obter uma visão geral das ferramentas de gerenciamento, consulte [Visão geral das ferramentas de gerenciamento](sql-database-manage-overview.md)
* Para saber como realizar tarefas de gerenciamento usando o Portal do Azure, consulte [Gerenciar Bancos de Dados SQL do Azure usando o Portal do Azure](sql-database-manage-portal.md)
* Para saber como realizar tarefas de gerenciamento usando o PowerShell, consulte [Gerenciar Bancos de Dados SQL do Azure usando o PowerShell](sql-database-manage-powershell.md)
* Para saber como realizar outras tarefas usando o SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obter informações sobre o serviço do Banco de Dados SQL, consulte [O que é o Banco de Dados SQL](sql-database-technical-overview.md). 
* Para obter informações sobre os servidores de Banco de Dados do Azure e recursos de banco de dados, confira [Recursos](sql-database-features.md).



<!--HONumber=Dec16_HO3-->


