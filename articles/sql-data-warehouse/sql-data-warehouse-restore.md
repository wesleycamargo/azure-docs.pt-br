---
title: Restaurar um SQL Data Warehouse do Azure | Microsoft Docs
description: Guia para restaurar um SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 112b9bac5d99556201b2038cf150293c9f2ed86d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005794"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Restaurar um SQL Data Warehouse do Azure 
Neste artigo, você aprenderá como fazer o seguinte no portal do Azure e no PowerShell:

- Criar um ponto de restauração
- Restaurar de um ponto de restauração automático ou de um ponto de restauração definido pelo usuário
- Restaurar de um banco de dados excluído
- Restaurar de um backup geográfico
- Criar uma cópia de seu data warehouse de um ponto de restauração definido pelo usuário

## <a name="before-you-begin"></a>Antes de começar
**Verifique sua capacidade de DTU.** Cada SQL Data Warehouse é hospedado por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma cota de DTU padrão.  Antes de restaurar um SQL Data Warehouse, verifique se o SQL Server tem cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU][Request a DTU quota change].

## <a name="restore-through-powershell"></a>Restaurar usando o PowerShell

## <a name="install-powershell"></a>Instalar o PowerShell
Para usar o Azure PowerShell com o SQL Data Warehouse, você precisará instalar a versão 1.0 ou superior do Azure PowerShell.  Você pode verificar a versão executando **Get-Module -ListAvailable -Name AzureRM**.  A versão mais recente pode ser instalada pelo [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Para obter mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database-using-powershell"></a>Restaurar um banco de dados ativo ou pausado usando o PowerShell
Para restaurar um banco de dados de um ponto de restauração, use o cmdlet do PowerShell [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

1. Abra o Windows PowerShell.

2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.

3. Selecione a assinatura que contém o banco de dados a ser restaurado.

4. Liste os pontos de restauração do banco de dados.

5. Selecione o ponto de restauração desejado usando o RestorePointCreationDate.

   > [!NOTE]
   > Ao restaurar, você pode especificar um ServiceObjectiveName (DWU) diferente ou um servidor diferente que reside em uma região diferente.

6. Restaure o banco de dados para o ponto de restauração desejado.

7. Verifique se o banco de dados restaurado está online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo [Configurar o banco de dados após a recuperação][Configure your database after recovery].
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points-using-powershell"></a>Copiar o data warehouse com pontos de restauração definidos pelo usuário usando o PowerShell
Para restaurar um banco de dados de um ponto de restauração definido pelo usuário, use o cmdlet do PowerShell [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Crie um ponto de restauração para uma cópia imediata do banco de dados
5. Renomeie seu banco de dados para um nome temporário.
6. Recupere o ponto de restauração mais recente pelo RestorePointLabel especificado.
7. Obtenha a ID do recurso do banco de dados para iniciar a restauração
8. Restaure o banco de dados para o ponto de restauração desejado.
9. Verifique se o banco de dados restaurado está online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzureRmSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database-using-powershell"></a>Restaurar um banco de dados excluído usando o PowerShell
Para restaurar um banco de dados excluído, use o cmdlet [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

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

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo [Configurar o banco de dados após a recuperação][Configure your database after recovery].
>

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Restaurar um banco de dados excluído usando o PowerShell
Para recuperar um banco de dados, use o cmdlet [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

> [!NOTE]
> Você pode executar uma restauração geográfica para Gen2! Para fazer isso, especifique um ServiceObjectiveName de Gen2 (por exemplo, DW1000**c**) como parâmetro opcional.
>

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Obtenha o banco de dados que você deseja recuperar.
5. Crie a solicitação de recuperação para o banco de dados.
6. Verifique o status do banco de dados com restauração geográfica.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Para configurar o banco de dados após a conclusão da restauração, veja [Configurar o banco de dados após a recuperação][Configure your database after recovery].
>

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.

## <a name="restore-through-the-azure-portal"></a>Restaurar através do portal do Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Criar um ponto de restauração definido pelo usuário usando o portal do Azure
1. Entre no [Portal do Azure][Azure portal].

2. Navegue até o SQL Data Warehouse para o qual você deseja criar um ponto de restauração.

3. Na parte superior da folha Visão Geral, selecione **+Novo Ponto de Restauração**.

    ![Novo Ponto de Restauração](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Especifique um nome para seu ponto de restauração.

    ![Nome do ponto de restauração](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Restaurar um banco de dados ativo ou pausado usando o portal do Azure
1. Entre no [Portal do Azure][Azure portal].
2. Navegue até o SQL Data Warehouse do qual você quer restaurar.
3. Na parte superior da folha Visão Geral, selecione **Restaurar**.

    ![ Visão Geral de Restauração](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Selecione **Pontos de Restauração Automática** ou **Pontos de Restauração Definido Pelo Usuário**.

    ![Pontos de restauração automática](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Para pontos de restauração definidos pelo usuário, **selecione um ponto de restauração** ou **crie um novo ponto de restauração definido pelo usuário**.

    ![Pontos de Restauração Definidos Pelo Usuário](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Restaurar um banco de dados excluído usando o portal do Azure
1. Entre no [Portal do Azure][Azure portal].
2. Navegue até o SQL Server em que o banco de dados excluído estava hospedado.
3. Selecione o ícone de bancos de dados excluídos no sumário.

    ![Bancos de dados excluídos](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)

4. Selecione o banco de dados excluído que você deseja restaurar.

    ![Selecione Bancos de Dados Excluídos](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)

5. Especifique um novo nome de banco de dados.

    ![Especifique o Nome do Banco de Dados](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
