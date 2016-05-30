<properties
   pageTitle="Fazer backup e restaurar um banco de dados no SQL Data Warehouse do Azure (PowerShell) | Microsoft Azure"
   description="Tarefas do PowerShell para restaurar um banco de dados dinâmico, excluído ou inacessível no SQL Data Warehouse do Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Fazer backup e restaurar um banco de dados no SQL Data Warehouse do Azure (PowerShell)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [Portal](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

Como fazer backup e restaurar um banco de dados no SQL Data Warehouse do Azure usando o PowerShell.

Tarefas neste tópico:

- Restaurar um banco de dados dinâmico
- Restaurar um banco de dados excluído
- Restaurar um banco de dados inacessível de outra região geográfica do Azure

[AZURE.INCLUDE [Política de retenção de backup do SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Antes de começar

### Verifique a capacidade de DTU do Banco de Dados SQL. 
Como o SQL Data Warehouse restaura para um novo banco de dados no SQL Server lógico, é importante verificar se o SQL Server para o qual você está restaurando tem capacidade de DTU suficiente para o novo banco de dados. Consulte esta postagem no blog para obter mais informações sobre [como exibir e aumentar a cota de DTU][].

### Instalar o PowerShell

Para usar o Azure PowerShell com o SQL Data Warehouse, você precisará instalar a versão 1.0 ou superior do Azure PowerShell. Você pode verificar a versão executando **Get-Module -ListAvailable -Name Azure**. A versão mais recente pode ser instalada pelo [Microsoft Web Platform Installer][]. Para obter mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][].

## Restaurar um banco de dados dinâmico

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

>[AZURE.NOTE] Para o servidor foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do Powershell acima.

Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo o guia [Finalizar um banco de dados recuperado][].

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

>[AZURE.NOTE] Para o servidor foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do Powershell acima.

Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo o guia [Finalizar um banco de dados recuperado][].

## Restaurar geograficamente um data warehouse por meio de um backup com redundância geográfica

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

### Configurar o banco de dados depois de executar uma restauração geográfica
Esta é uma lista de verificação para ajudar a preparar para produção seu banco de dados recuperado.

1. **Atualizar Cadeias de Conexão**: verifique se as cadeias de conexão das ferramentas cliente estão apontando para o banco de dados recentemente recuperado.
2. **Modificar as Regras de Firewall**: verifique as regras de firewall no servidor de destino para ter certeza de que as conexões dos computadores cliente ou do Azure com o servidor e com o banco de dados recentemente recuperado estão habilitadas.
3. **Verificar Logons do Servidor e Usuários do Banco de Dados**: verifique se todos os logons usados pelo aplicativo existem no servidor que está hospedando o banco de dados recuperado. Recrie os logons ausentes e conceda a eles permissões apropriadas no banco de dados recuperado. 
4. **Habilitar a Auditoria**: se a auditoria for necessária para acessar o banco de dados, você precisará habilitar a Auditoria após a recuperação do banco de dados.

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.


## Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral de continuidade de negócios do Banco de Dados SQL do Azure][].

<!--Image references-->

<!--Article references-->
[Visão geral de continuidade de negócios do Banco de Dados SQL do Azure]: sql-database-business-continuity.md
[Finalizar um banco de dados recuperado]: sql-database-recovered-finalize.md
[Como instalar e configurar o Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[como exibir e aumentar a cota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->