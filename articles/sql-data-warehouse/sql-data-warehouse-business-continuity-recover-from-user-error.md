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
   ms.date="03/23/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Recuperar um banco de dados de um erro do usuário no SQL Data Warehouse

O SQL Data Warehouse oferece dois recursos principais para a recuperação de erro do usuário que causa a exclusão de dados ou dados corrompidos não intencionais:

- Restaurar um banco de dados dinâmico
- Restaurar um banco de dados excluído

Os dois recursos restauram um novo banco de dados no mesmo servidor.

Há duas APIs diferentes que oferecem suporte a uma restauração de banco de dados do SQL Data Warehouse: API REST e Azure PowerShell. Você pode usar qualquer um para acessar a funcionalidade de restauração do SQL Data Warehouse.

## Restaurar um banco de dados dinâmico
No caso de erro de usuário causando modificações de dados não intencionais, você pode restaurar o banco de dados para qualquer um dos pontos de restauração dentro do período de retenção. Os instantâneos de banco de dados para um banco de dados dinâmico ocorrem, pelo menos, a cada 8 horas e são mantidos por 7 dias.

### PowerShell

Use o Azure PowerShell para executar a restauração de banco de dados programaticamente. Para baixar o módulo PowerShell do Azure, execute o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Você pode verificar a versão executando Get-Module -ListAvailable -Name Azure. Este artigo baseia-se na versão 1.0.4 do Microsoft Azure PowerShell.

Para restaurar um banco de dados, use o cmdlet [Start-AzureSqlDatabaseRestore][].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Liste os pontos de restauração do banco de dados (requer o modo de gerenciamento de recursos do Azure).
5. Selecione o ponto de restauração desejado usando o RestorePointCreationDate.
6. Restaure o banco de dados para o ponto de restauração desejado.
7. Monitore o progresso da restauração.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>").RestorePointCreationDate)[-10 .. -1]

	# Or for all restore points
	Get-AzureRmSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database name to restore
(Get-AzureRmSqlDatabase -ServerName "<YourServerName>" -ResourceGroupName "<YourResourceGroupName>").DatabaseName | where {$_ -ne "master" }
#or
Get-AzureRmSqlDatabase -ServerName "<YourServerName>" –ResourceGroupName "<YourResourceGroupName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabaseName "<YourDatabaseName>" -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Observe que, se o servidor for foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do Powershell acima.

### API REST
Use a API REST para executar a restauração do banco de dados de modo programático.

1. Obtenha a lista de pontos de restauração do banco de dados usando a operação de obtenção de pontos de restauração de banco de dados.
2. Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados][].
3. Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados][].

Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado a ser usado de acordo com o guia [Finalizar um banco de dados recuperado][].

## Recuperar um banco de dados excluído
No caso de um banco de dados ser excluído, você pode restaurar o banco de dados excluído para a hora da exclusão. O SQL Data Warehouse do Azure captura um instantâneo antes do banco de dados ser descartado e o retém por sete dias.

### PowerShell
Usar o Azure PowerShell para executar uma restauração de banco de dados excluídos de forma programática. Para baixar o módulo PowerShell do Azure, execute o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).

Para restaurar um banco de dados excluído, use o cmdlet [Start-AzureSqlDatabaseRestore][].

1. Abra o Microsoft Azure PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados excluído a ser restaurado.
4. Localize o banco de dados e sua data de exclusão da lista de bancos de dados excluídos

```Powershell
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. Obtenha o banco de dados excluído e inicie a restauração.

```Powershell
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Observe que, se o servidor for foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do Powershell acima.

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
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->