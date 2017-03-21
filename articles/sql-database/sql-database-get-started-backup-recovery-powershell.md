---
title: 'PowerShell: fazer backup e restaurar um banco de dados SQL do Azure | Microsoft Docs'
description: "Este tutorial mostra como restaurar a partir de backups automatizados em um ponto no tempo, armazenar os backups automáticos no cofre dos Serviços de Recuperação do Azure e restaurar a partir do cofre dos Serviços de Recuperação do Azure usando o PowerShell"
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 12/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 85897322bc341f549c52dbe4758e8c7f860df22c
ms.lasthandoff: 03/10/2017


---


# <a name="tutorial-back-up-and-restore-an-azure-sql-database-using-powershell"></a>Tutorial: fazer backup e restaurar um Banco de Dados SQL do Azure usando o PowerShell

Neste tutorial, você aprenderá a usar o Azure PowerShell para:

- Exibir os backups existentes de um banco de dados
- Restaurar um banco de dados em um ponto anterior no tempo
- Configurar a retenção de longo prazo de um arquivo de backup do banco de dados no cofre dos Serviços de Recuperação do Azure
- Restaurar um banco de dados a partir do cofre dos Serviços de Recuperação do Azure

**Tempo estimado**: este tutorial levará cerca de 30 minutos para concluir (supondo que você já atendeu aos pré-requisitos).


## <a name="prerequisites"></a>Pré-requisitos

* **Uma conta do Azure**. Você precisa de uma conta do Azure. Você pode [abrir uma conta gratuita do Azure](https://azure.microsoft.com/free/) ou [Ativar os benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **O Azure cria permissões**. Você deve ser capaz de se conectar ao portal do Azure usando uma conta que seja membro da função de colaborador ou proprietário da assinatura. Para obter mais informações sobre o controle de acesso baseado em função (RBAC), confira [Introdução ao gerenciamento de acesso no Portal do Azure](../active-directory/role-based-access-control-what-is.md).

* **PowerShell** Você precisa ter o Azure PowerShell mais recente instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs).

* **SQL Server Management Studio**. Você pode baixar e instalar a versão mais recente do SSMS (SQL Server Management Studio) em [Baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Sempre use a versão mais recente do SSMS ao se conectar ao Banco de Dados SQL do Azure, já que sempre há o lançamento de novos recursos.

* **Servidor e bancos de dados base** para instalar e configurar um servidor e os dois bancos de dados usados neste tutorial, clique no botão **Implantar no Azure**. Clicar no botão abre a folha **Implantar de um modelo**; crie um novo grupo de recursos e forneça a **Senha de Logon de Administrador** para o novo servidor que será criado:

   [![download](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fsqldbtutorial.blob.core.windows.net%2Ftemplates%2Fsqldbgetstarted.json)

> [!TIP]
> Você pode executar as mesmas tarefas em um tutorial de introdução usando o [portal do Azure](sql-database-get-started-backup-recovery-portal.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>Exibir o ponto de restauração mais antigo a partir dos backups gerados pelo serviço de um banco de dados

Nesta seção do tutorial, você exibe informações sobre o ponto de restauração mais antigo dos [backups automatizados gerados pelo serviço](sql-database-automated-backups.md) do banco de dados. 

Você pode restaurar um banco de dados para qualquer ponto no tempo entre o ponto de restauração mais antigo e o último backup disponível (seis minutos antes da hora atual). 

O trecho a seguir usa [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabase) para obter o ponto de restauração mais antigo do banco de dados que você deseja restaurar. A hora é retornada como UTC, mas os trechos de código a seguir mostram como trabalhar no horário local. O ponto de restauração mais recente disponível de um banco de dados ativo normalmente é de cerca de seis minutos atrás. Portanto, o ponto de restauração mais recente é simplesmente definido como a hora atual menos seis minutos. 

```
# Get available restore points

$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseName = "{database-name}"

$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"
```



## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados em um ponto anterior no tempo

Nesta seção do tutorial, você restaura o banco de dados para um novo banco de dados a partir de um ponto específico no tempo. 

>[!NOTE]
>Você não pode alterar o servidor no qual está restaurando para um ponto específico no tempo. Para restaurar em um servidor diferente, use a [Restauração Geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore). Além disso, observe que você pode restaurar para um [pool de banco de dados elásticos](sql-database-elastic-jobs-overview.md) ou para um tipo de preço diferente. 

O trecho a seguir usa o cmdlet [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) para restaurar o $databaseToRestore que definimos no trecho anterior. O parâmetro **-PointInTime** requer um valor de hora UTC formatada semelhante a: *09/12/2016 20:00:00*. O trecho de código converte a hora local para você.

```
# Restore a database to a previous point in time

#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}
$newRestoredDatabaseName = "{new-database-name}"
$localTimeToRestoreTo = "{12/9/2016 12:00:00 PM}" # change to a valid restore point for your database
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb
```

> [!NOTE]
> A partir daqui, você pode conectar o banco de dados restaurado usando o [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) para executar as tarefas necessárias, tais como [extrair um pouco de dados do banco de dados restaurado para copiar para o banco de dados existente ou excluir o banco de dados existente e renomear o banco de dados restaurado com o nome do banco de dados existente](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Configurar a retenção de longo prazo dos backups automatizados em um cofre dos Serviços de Recuperação do Azure 

Nesta seção do tutorial, você [configura um cofre dos Serviços de Recuperação do Azure para manter os backups automatizados](sql-database-long-term-retention.md) por um período maior que o período de retenção da camada de serviços (até 10 anos). 


> [!TIP]
> Para excluir backups de retenção de longo prazo, consulte [Gerenciar a retenção de backup de longo prazo, usando o PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).


### <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

> [!IMPORTANT]
> O cofre deve estar localizado na mesma região do servidor lógico do SQL Azure e deve usar o mesmo grupo de recursos como o servidor lógico.

O trecho a seguir usa os cmdlets [New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault) e [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties) para criar um novo cofre de serviços de recuperação e definir o nível de redundância para backups no cofre. 

```
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-you-just-created-for-its-long-term-retention-backups"></a>Definir seu servidor para usar o cofre de recuperação que você acabou de criar para backups de retenção de longo prazo

O trecho a seguir usa o cmdlet [Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault) para associar o cofre de serviços de recuperação criado anteriormente a um servidor específico do SQL Azure.

```
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Criar uma política de retenção

Em uma política de retenção, você define por quanto tempo um backup de banco de dados deve ser mantido. 

O trecho a seguir usa [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) para obter a política de retenção padrão que usamos como modelo para criar novas políticas. Definimos nosso modelo para manter o backup por dois anos e executar [New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy) para criar finalmente a nova política. 

Observe que alguns cmdlets exigem que você define o contexto de cofre antes da execução ([Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesvaultcontext)) para ver esse cmdlet em alguns trechos de código relacionados. Definimos o contexto porque a política faz parte do cofre. Você pode criar várias políticas de retenção para cada compartimento e aplicar a política desejada a bancos de dados específicos. 


```
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Configurar um banco de dados para usar a política de retenção definida anteriormente

O trecho a seguir usa o cmdlet [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy) para aplicar a nova política a um banco de dados específico.

```
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

> [!IMPORTANT]
> Uma vez configurados, os backups aparecerão no cofre nos próximos sete dias. Continue este tutorial depois que os backups forem mostrados no cofre.

## <a name="view-backup-info-and-backups-in-long-term-retention"></a>Exibir informações de backup e backups de retenção de longo prazo

Nesta seção do tutorial, você exibe informações sobre os backups do banco de dados na [retenção de backup de longo prazo](sql-database-long-term-retention.md). 

Os trechos a seguir consultam informações do cofre usando os cmdlets [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer), [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem) e [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint).

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```


## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restaurar um banco de dados a partir de um backup na retenção de backup de longo prazo

Nesta seção do tutorial, você restaura o banco de dados para um novo banco de dados a partir de um backup no cofre dos Serviços de Recuperação do Azure.

Assim como os snippets de ponto no tempo, anteriormente neste tutorial, a restauração da retenção de backup de longo prazo usa o cmdlet [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase), exceto que, desta vez, usa o parâmetro **-FromLongTermRetentionBackup** (em vez do parâmetro **-FromPointInTimeBackup** usado anteriormente).

```
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> A partir daqui, você pode conectar o banco de dados restaurado usando o SQL Server Management Studio para executar as tarefas necessárias, tais como, [extrair um pouco de dados do banco de dados restaurado para copiar para o banco de dados existente ou excluir o banco de dados existente e renomear o banco de dados restaurado com o nome do banco de dados existente](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="complete-azure-powershell-script-to-restore-from-a-previous-point-in-time-and-to-configure-and-restore-from-long-term-backup-retention-using-powershell"></a>Script completo do Azure PowerShell para restaurar de um ponto anterior no tempo e para configurar e restaurar da retenção de backup de longo prazo, usando o PowerShell

> [!NOTE]
> Ao tentar restaurar o backup mais recente no fim desse script, você obterá uma exceção *Não é possível indexar uma matriz nula* se não houver backups no cofre.

```
# Sign in to Azure and set the subscription to work with
########################################################

$subscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId


# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myServerName = "{server-name}"
$myDatabaseToRestoreFromPointInTime = "{database-name}"
$myLocalTimeToRestoreTo = "{12/08/2016 16:00:00}" # change to a valid restore point for your database
$myNewDatabaseRestoredFromPointInTime = "{new-database-name}"

$myRecoveryServiceVaultName = "{vault-name}"
$myRetentionPolicyDurationType = "{duration-period}" # set to Years, Months, or Weeks
$myRetentionPolicyDuration = {2}
$myRetentionPolicyName = "{retention-policy-name}"
$myDatabaseToRestoreFromLTR = "{database-name}"
$myNewDatabaseRestoredFromLTR = "{new-database-name}"


# Get available restore points for a specific database
######################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseToRestoreFromPointInTime


$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"


# Restore a database to a previous point in time
################################################

$newRestoredDatabaseName = $myNewDatabaseRestoredFromPointInTime
$localTimeToRestoreTo = $myLocalTimeToRestoreTo
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb



# CONFIGURE LONG-TERM RETENTION

# Create a recovery services vault
##################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = $myRecoveryServiceVaultName

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
$vault

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Retrieve the default retention policy for the AzureSQLDatabase workload type
##############################################################################

$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values
$retentionPolicy.RetentionDurationType = $myRetentionPolicyDurationType
$retentionPolicy.RetentionCount = $myRetentionPolicyDuration

$retentionPolicyName = $myRetentionPolicyName

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy

$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id


$databaseNeedingRestore = $myDatabaseToRestoreFromLTR

# Set the vault context to the vault we want to restore from
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Get the container associated with your vault
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
###

# This command restores the most recent backup: $availableBackups[0]
$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$restoredDatabaseName = $myNewDatabaseRestoredFromLTR
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDbFromLtr = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel

$restoredDbFromLtr
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre backups automáticos gerados pelo serviço, veja [backups automáticos](sql-database-automated-backups.md)
- Para saber mais sobre a retenção de backup de longo prazo, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md)
- Para saber mais sobre a restauração a partir de backups, consulte [restaurar a partir do backup](sql-database-recovery-using-backups.md)

