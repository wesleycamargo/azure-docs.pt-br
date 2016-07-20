<properties
   pageTitle="Restaurar um Azure SQL Data Warehouse (PowerShell) | Microsoft Azure"
   description="Tarefas do PowerShell para restaurar um Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/28/2016"
   ms.author="sonyama;barbkess"/>

# Restaurar um Azure SQL Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Visão geral][]
- [Portal][]
- [PowerShell][]
- [REST][]

Neste artigo, você aprenderá como restaurar um Azure SQL Data Warehouse usando o PowerShell.

## Antes de começar

### Verifique a capacidade de DTU do Banco de Dados SQL. 

Cada SQL Data Warehouse é hospedado por um servidor lógico SQL Server. Esse servidor lógico tem um limite de capacidade medido em DTUs. Antes de restaurar um SQL Data Warehouse, é importante verificar se o servidor lógico SQL Server que hospeda seu banco de dados tem capacidade DTU suficiente para o banco de dados que está sendo restaurado. Confira esta postagem no blog para saber mais sobre [como exibir e aumentar a cota de DTU][].

### Instalar o PowerShell

Para usar o Azure PowerShell com o SQL Data Warehouse, você precisará instalar a versão 1.0 ou superior do Azure PowerShell. Você pode verificar a versão executando **Get-Module -ListAvailable -Name AzureRM**. A versão mais recente pode ser instalada pelo [Microsoft Web Platform Installer][]. Para obter mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][].

## Restaurar um banco de dados ativo ou pausado

Para restaurar um banco de dados por meio de um instantâneo, use o cmdlet [Restore-AzureRmSqlDatabase][] do PowerShell.

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Liste os pontos de restauração do banco de dados.
5. Selecione o ponto de restauração desejado usando o RestorePointCreationDate.
6. Restaure o banco de dados para o ponto de restauração desejado.
7. Verifique se o banco de dados restaurado está online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo o guia [Finalizar um banco de dados recuperado][].


## Restaurar um banco de dados excluído

Para restaurar um banco de dados excluído, use o cmdlet [Restore-AzureRmSqlDatabase][].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados excluído a ser restaurado.
4. Obtenha o banco de dados excluído em questão.
5. Restaure o banco de dados excluído.
6. Verifique se o banco de dados restaurado está online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo o guia [Finalizar um banco de dados recuperado][].


## Restaurar por meio de uma região geográfica do Azure

Para recuperar um banco de dados, use o cmdlet [Restore-AzureRmSqlDatabase][].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Obtenha o banco de dados que você deseja recuperar.
5. Crie a solicitação de recuperação para o banco de dados.
6. Verifique o status do banco de dados com restauração geográfica.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo o guia [Finalizar um banco de dados recuperado][].


O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.


## Próximas etapas
Para saber mais sobre os recursos de continuidade dos negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral da continuidade dos negócios do Banco de Dados SQL do Azure][].

<!--Image references-->

<!--Article references-->
[Visão geral da continuidade dos negócios do Banco de Dados SQL do Azure]: sql-database-business-continuity.md
[Finalize a recovered database]: sql-database-recovered-finalize.md
[Como instalar e configurar o Azure PowerShell]: powershell-install-configure.md
[Visão geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Finalizar um banco de dados recuperado]: ./sql-database-recovered-finalize.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[como exibir e aumentar a cota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0706_2016-->