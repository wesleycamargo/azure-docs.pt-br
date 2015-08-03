<properties
   pageTitle="Recuperar um banco de dados de um erro do usuário no SQL Data Warehouse | Microsoft Azure"
   description="Etapas para recuperar um banco de dados de um erro do usuário no SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="sahajs"/>

# Recuperar um banco de dados de um erro do usuário no SQL Data Warehouse

O SQL Data Warehouse oferece dois recursos principais para a recuperação de erro do usuário que causa a exclusão de dados ou dados corrompidos não intencionais:

- Restaurar um banco de dados dinâmico
- Restaurar um banco de dados excluído

Os dois recursos restauram um novo banco de dados no mesmo servidor.

## Restaurar um banco de dados dinâmico
No caso de erro de usuário causando modificações de dados não intencionais, você pode restaurar o banco de dados para qualquer um dos pontos de restauração dentro do período de retenção. Os instantâneos de banco de dados para um banco de dados dinâmico ocorrem a cada 8 horas e são mantidos por 7 dias.

### PowerShell

Use o PowerShell para executar a restauração do banco de dados de modo programático. Para restaurar um banco de dados, use o cmdlet [Start-AzureSqlDatabaseRestore][].

1. Selecione a assinatura na sua conta que contém o banco de dados a ser restaurado.
2. Liste os pontos de restauração do banco de dados (requer o modo de Gerenciamento de Recursos do Azure)
3. Selecione o ponto de restauração desejado usando o RestorePointCreationDate.
3. Restaure o banco de dados para o ponto de restauração desejado.
4. Monitore o progresso da restauração.

```
Select-AzureSubscription -SubscriptionId <Subscription_GUID>

# List database restore points
Switch-AzureMode AzureResourceManager
Get-AzureSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
Switch-AzureMode AzureServiceManagement
$Database = Get-AzureSqlDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

### API REST
Use a API REST para executar a restauração do banco de dados de modo programático.

1. Obtenha a lista de pontos de restauração do banco de dados usando a operação de obtenção de pontos de restauração de banco de dados.
2. Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados][].
3. Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados][].

Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado a ser usado de acordo com o guia [Finalizar um banco de dados recuperado][].

## Recuperar um banco de dados excluído
No caso de um banco de dados ser excluído, você pode restaurar o banco de dados excluído para a hora da exclusão. O SQL Data Warehouse do Azure captura um instantâneo antes do banco de dados ser descartado e o retém por sete dias.

### PowerShell
Use o PowerShell para executar com programação uma restauração de banco de dados excluído. Para restaurar um banco de dados excluído, use o cmdlet [Start-AzureSqlDatabaseRestore][].

1. Localize o banco de dados excluído e a data da exclusão na lista de bancos de dados excluídos.

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

2. Obtenha o banco de dados excluído e inicie a restauração.

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

### API REST
Use a API REST para executar a restauração do banco de dados de modo programático.

1.	Liste todos os bancos de dados excluídos restauráveis usando a operação [Listar bancos de dados descartados restauráveis][].
2.	Obtenha os detalhes do banco de dados excluído que deseja restaurar usando a operação [Obter banco de dados descartados restauráveis][].
3.	Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados][].
4.	Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados][].

Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado a ser usado de acordo com o guia [Finalizar um banco de dados recuperado][].


## Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios de outras edições do Banco de Dados SQL do Azure, leia a [Visão geral de continuidade de negócios do Banco de Dados SQL do Azure][].


<!--Image references-->

<!--Article references-->
[Visão geral de continuidade de negócios do Banco de Dados SQL do Azure]: sql-database/sql-database-business-continuity.md
[Finalizar um banco de dados recuperado]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Criar solicitação de restauração do banco de dados]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Status de operações do banco de dados]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Obter banco de dados descartados restauráveis]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[Listar bancos de dados descartados restauráveis]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/en-us/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=July15_HO4-->