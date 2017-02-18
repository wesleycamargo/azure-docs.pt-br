---
title: 'PowerShell: Criar e gerenciar servidores de Banco de Dados SQL do Azure | Microsoft Docs'
description: "Referência rápida sobre como criar e gerenciar servidores de banco de dados SQL com o PowerShell."
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
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 835702c8381fcd184b056c76054514348c675bbf
ms.openlocfilehash: 9f1905f7e5aad0e43d81f7095089b9f3492a82ea


---
 
# <a name="create-and-manage-azure-sql-database-servers-with-powershell"></a>Criar e gerenciar servidores de banco de dados SQL com o PowerShell

Você pode criar e gerenciar um servidor de Banco de Dados SQL do Azure com o [portal do Azure](https://portal.azure.com/), o PowerShell, a API REST ou o C#. Este tópico mostra como usar o PowerShell. Para o portal do Azure, veja [criar e gerenciar servidores usando o portal do Azure](sql-database-manage-servers-portal.md). 

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
> Para obter um script de exemplo, veja [Criar um banco de dados SQL usando um script do PowerShell](sql-database-get-started-powershell.md).
>

## <a name="additional-resources"></a>Recursos adicionais
* Para obter uma visão geral das ferramentas de gerenciamento, consulte [Visão geral das ferramentas de gerenciamento](sql-database-manage-overview.md)
* Para saber como realizar tarefas de gerenciamento usando o Portal do Azure, consulte [Gerenciar Bancos de Dados SQL do Azure usando o Portal do Azure](sql-database-manage-portal.md)
* Para saber como realizar tarefas de gerenciamento usando o PowerShell, consulte [Gerenciar Bancos de Dados SQL do Azure usando o PowerShell](sql-database-manage-powershell.md)
* Para saber como realizar outras tarefas usando o SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obter informações sobre o serviço do Banco de Dados SQL, consulte [O que é o Banco de Dados SQL](sql-database-technical-overview.md). 
* Para obter informações sobre os servidores de Banco de Dados do Azure e recursos de banco de dados, confira [Recursos](sql-database-features.md).



<!--HONumber=Feb17_HO1-->


