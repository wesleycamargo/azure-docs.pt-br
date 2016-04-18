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
   ms.date="03/28/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Recuperar um banco de dados de um erro do usuário no SQL Data Warehouse

O SQL Data Warehouse oferece dois recursos principais para a recuperação de erro do usuário que causa a exclusão de dados ou dados corrompidos não intencionais:

- Restaurar um banco de dados dinâmico
- Restaurar um banco de dados excluído

Os dois recursos restauram um novo banco de dados no mesmo servidor. É importante garantir que o servidor que você está restaurando tenha capacidade de DTU suficiente para o novo banco de dados. Você pode solicitar um aumento dessa cota [contatando o suporte][].


## Restaurar um banco de dados dinâmico
O serviço SQL Data Warehouse do Azure protege todos os bancos de dados ativos com instantâneos deles a pelo menos cada oito horas e os mantêm por sete dias para fornecer a você um conjunto distinto de pontos de restauração. Instantâneos dos banco de dados também são criados e mantidos por sete dias quando você pausa ou remove o banco de dados. No caso de erro de usuário causando modificações de dados não intencionais, você pode restaurar o banco de dados para qualquer um dos pontos de restauração dentro do período de retenção.


### Portal do Azure

Para restaurar usando o Portal do Azure, empregue as etapas a seguir.

1. Faça logon no [Portal do Azure][].
2. No lado esquerdo da tela, selecione **PROCURAR** e, em seguida, **Bancos de Dados SQL**.
3. Navegue até o banco de dados e selecione-o.
4. Na parte superior da folha do banco de dados, clique em **Restaurar**.
5. Especifique um novo **Nome do banco de dados**, selecione um **Ponto de Restauração** e clique em **Criar**.
6. O processo de restauração do banco de dados começará e poderá ser monitorado usando as **NOTIFICAÇÕES**.


### PowerShell

Use o Azure PowerShell para executar uma restauração de banco de dados programaticamente. Para baixar o módulo PowerShell do Azure, execute o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Você pode verificar sua versão executando Get-Module -ListAvailable -Name AzureRM.Sql. Este artigo baseia-se na versão 1.0.5 do Microsoft AzureRM.Sql PowerShell.

Para restaurar um banco de dados, use o cmdlet [Restore-AzureRmSqlDatabase][].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Liste os pontos de restauração do banco de dados.
5. Selecione o ponto de restauração desejado usando o RestorePointCreationDate.
6. Restaure o banco de dados para o ponto de restauração desejado.
7. Verifique se o banco de dados restaurado está online.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>").RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" 

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "<NewDatabaseName>" –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Para o servidor foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do Powershell acima.


### API REST
Use a API REST para executar a restauração do banco de dados de modo programático.

1. Obtenha a lista de pontos de restauração do banco de dados usando a operação de obtenção de pontos de restauração de banco de dados.
2. Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados][].
3. Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados][].


>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado usando o guia [Finalizar um banco de dados recuperado][].


## Recuperar um banco de dados excluído
O SQL Data Warehouse do Azure captura um instantâneo antes de um banco de dados ser descartado e o retém por sete dias. No caso de um banco de dados ser excluído acidentalmente, você pode restaurá-lo para a hora da exclusão.


### Portal do Azure

Para restaurar um banco de dados excluído usando o Portal do Azure, empregue as etapas a seguir.

1. Faça logon no [Portal do Azure][].
2. No lado esquerdo da tela, selecione **PROCURAR** e, em seguida, **Servidores SQL**.
3. Navegue até o servidor e selecione-o.
4. Role para baixo até Operações na folha do servidor e clique no bloco **Bancos de Dados Excluídos**.
5. Selecione o banco de dados excluído que deseja restaurar.
5. Especifique um novo **Nome de banco de dados** e clique em **Criar**.
6. O processo de restauração do banco de dados começará e poderá ser monitorado usando as **NOTIFICAÇÕES**.


### PowerShell
Usar o Azure PowerShell para executar uma restauração de banco de dados excluídos de forma programática. Para baixar o módulo PowerShell do Azure, execute o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Você pode verificar sua versão executando Get-Module -ListAvailable -Name AzureRM.Sql. Este artigo baseia-se na versão 1.0.5 do Microsoft AzureRM.Sql PowerShell.

Para restaurar um banco de dados excluído, use o cmdlet [Restore-AzureRmSqlDatabase][].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados excluído a ser restaurado.
4. Obtenha o banco de dados excluído em questão.
5. Restaure o banco de dados excluído.
6. Verifique se o banco de dados restaurado está online.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "<NewDatabaseName>" –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Para o servidor foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do Powershell acima.


### API REST
Use a API REST para executar a restauração do banco de dados de modo programático.

1.	Liste todos os bancos de dados excluídos restauráveis usando a operação [Listar bancos de dados descartados restauráveis][].
2.	Obtenha os detalhes do banco de dados excluído que deseja restaurar usando a operação [Obter banco de dados descartados restauráveis][].
3.	Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados][].
4.	Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados][].


>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado usando o guia [Finalizar um banco de dados recuperado][].


## Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral de continuidade de negócios do Banco de Dados SQL do Azure][].


<!--Image references-->

<!--Article references-->
[Visão geral de continuidade de negócios do Banco de Dados SQL do Azure]: sql-database/sql-database-business-continuity.md
[Finalizar um banco de dados recuperado]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Criar solicitação de restauração do banco de dados]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Status de operações do banco de dados]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Obter banco de dados descartados restauráveis]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[Listar bancos de dados descartados restauráveis]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Portal do Azure]: https://portal.azure.com/
[contatando o suporte]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0406_2016-->