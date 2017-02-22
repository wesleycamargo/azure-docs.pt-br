---
title: Gerenciar o Banco de Dados SQL do Azure com o PowerShell | Microsoft Docs
description: Gerenciamento de banco de dados SQL do Azure com o PowerShell.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 32d8c5f13d95c3de8b06782f4f6541866389be5b


---
# <a name="managing-azure-sql-database-using-powershell"></a>Gerenciar um Banco de Dados SQL do Azure usando o PowerShell

Este tópico mostra os cmdlets do PowerShell que são usados para executar várias tarefas do Banco de Dados SQL do Azure. Para ver uma lista completa, veja [Cmdlets do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx).

> [!TIP]
> Para obter um tutorial que mostra como criar um servidor, criar um firewall baseado em servidor, exibir propriedades do servidor, conectar e consultar o banco de dados mestre, criar um banco de dados de exemplo e um banco de dados em branco, consultar propriedades de banco de dados, conectar-se e consultar o banco de dados de exemplo, confira [Tutorial de Introdução](sql-database-get-started-powershell.md).
>

## <a name="how-do-i-create-a-resource-group"></a>Como crio um grupo de recursos?
Para criar um grupo de recursos para seu Banco de Dados SQL e para recursos relacionados do Azure, use o cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx) .

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Para obter mais informações, consulte [Usando o PowerShell do Azure com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md).
Para obter um tutorial completo, confira [Introdução aos servidores de Banco de Dados SQL do Azure, bancos de dados e regras de firewall usando o Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server"></a>Como criar um servidor de Banco de Dados SQL?
Para criar um servidor de Banco de Dados SQL, use o cmdlet [New-AzureRmSqlServer](/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). Substitua *server1* pelo nome do seu servidor. Os nomes dos servidores devem ser exclusivos para todos os servidores do Banco de Dados SQL do Azure. Se o nome do servidor já existir, você obterá um erro. Esse comando pode levar vários minutos para ser concluído. O grupo de recursos já precisa existir na sua assinatura.

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

Para obter mais informações sobre servidores, confira [Recursos de Banco de Dados SQL](sql-database-features.md). Para obter um tutorial completo, confira [Introdução aos servidores de Banco de Dados SQL do Azure, bancos de dados e regras de firewall usando o Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>Como crio uma regra de firewall do servidor do Banco de Dados SQL?
Para criar uma regra de firewall para acessar o servidor, use o cmdlet [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). Execute o comando a seguir substituindo os endereços IP inicial e final pelos valores válidos para o seu cliente. O grupo de recursos e o servidor precisam já existir em sua assinatura.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Para permitir que outros serviços do Azure acessem seu servidor, crie uma regra de firewall e defina `-StartIpAddress` e `-EndIpAddress` como **0.0.0.0**. Essa regra de firewall especial permite que todo o tráfego do Azure acesse o servidor.

Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Para obter um tutorial completo, confira [Introdução aos servidores de Banco de Dados SQL do Azure, bancos de dados e regras de firewall usando o Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database"></a>Como crio um Banco de Dados SQL?
Para criar um banco de dados SQL, use o cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx). O grupo de recursos e o servidor precisam já existir em sua assinatura. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para saber mais, veja [O que é o Banco de Dados SQL](sql-database-technical-overview.md). Para obter um tutorial completo, confira [Introdução aos servidores de Banco de Dados SQL do Azure, bancos de dados e regras de firewall usando o Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>Como posso alterar o nível de desempenho do banco de dados SQL?
Para alterar o nível de desempenho, escale seu banco de dados verticalmente ou horizontalmente com o cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx). O grupo de recursos, o servidor e o banco de dados precisam já existir em sua assinatura. Defina `-RequestedServiceObjectiveName` como um espaço simples (como o trecho a seguir) para a camada Basic. Defina-o como *S0*, *S1*, *P1*, *P6*, etc., como no exemplo anterior, para outras camadas.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para saber mais, veja [Opções e desempenho de Banco de Dados SQL: saiba o que está disponível em cada camada de serviço](sql-database-service-tiers.md). Para obter um script de exemplo, veja [Amostra de script do PowerShell para alterar a camada de serviços e o nível de desempenho do banco de dados SQL](sql-database-manage-single-databases-powershell.md#change-the-service-tier-and-performance-level-of-a-single-database).

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>Como copio um banco de dados SQL para o mesmo servidor?
Para copiar um banco de dados SQL para o mesmo servidor, use ocmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx). Defina `-CopyServerName` e `-CopyResourceGroupName` com os mesmos valores que seu grupo de recursos e servidor de banco de dados de origem.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Para saber mais, confira [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md). Para obter um script de exemplo, veja [Copiar um script do PowerShell do banco de dados SQL](sql-database-copy-powershell.md#example-powershell-script).

## <a name="how-do-i-delete-a-sql-database"></a>Como excluir um banco de dados SQL?
Para excluir um banco de dados SQL. use o cmdlet [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx). O grupo de recursos, o servidor e o banco de dados precisam já existir em sua assinatura.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>Como excluo um servidor de Banco de Dados SQL?
Para excluir um servidor de Banco de Dados SQL, use o cmdlet [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>Como criar e gerenciar pools elásticos usando o PowerShell?
Para obter detalhes sobre como criar pools elásticos usando o PowerShell, confira [Criar um novo pool elástico com o PowerShell](sql-database-elastic-pool-manage-powershell.md).

Para obter detalhes sobre como gerenciar pools elásticos usando o PowerShell, confira [Monitorar e gerenciar um pool elástico com o PowerShell](sql-database-elastic-pool-manage-powershell.md).

## <a name="related-information"></a>Informações relacionadas
* [Cmdlets do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Referência de Cmdlets do Azure](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Feb17_HO3-->


