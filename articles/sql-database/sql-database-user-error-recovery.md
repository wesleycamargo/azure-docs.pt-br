<properties 
   pageTitle="Recuperação de erro do usuário no Banco de Dados SQL" 
   description="Saiba como fazer a recuperação de um erro do usuário, da corrupção de dados acidental ou de um banco de dados excluído usando o recurso PITR (Restauração Pontual) do Banco de Dados SQL do Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="10/08/2015"
   ms.author="elfish"/>

# Recuperar um Banco de Dados SQL do Azure de um erro do usuário

O Banco de Dados SQL do Azure oferece dois recursos principais para recuperação de erro do usuário ou modificação de dados não intencional.

- Restauração Pontual 
- Restaurar banco de dados excluído

Você pode aprender mais sobre esses recursos nesta [postagem de blog](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/).

O Banco de Dados SQL do Azure sempre restaura para um novo banco de dados. Esses recursos de restauração são oferecidos para todos os bancos de dados Basic, Standard e Premium.
##Recuperação com Restauração Pontual
No caso de um erro do usuário ou uma modificação de dados não intencional, a Restauração Pontual pode ser usada para fazer uma restauração pontual do banco de dados dentro do período de retenção dos bancos de dados.

Os bancos de dados Basic tem 7 dias de retenção, os bancos de dados Standard tem 14 dias de retenção e os bancos de dados Premium têm 35 dias de retenção. Para saber mais sobre a retenção de banco de dados, leia nossa [visão geral da continuidade dos negócios](sql-database-business-continuity.md).

> [AZURE.NOTE]A restauração de um banco de dados cria um novo banco de dados. É importante garantir que o servidor que você está restaurando tenha capacidade de DTU suficiente para o novo banco de dados. Você pode solicitar um aumento dessa cota [contatando o suporte](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portal do Azure
1. Faça logon no [Portal do Azure](https://portal.Azure.com)
2. No lado esquerdo da tela, selecione **PROCURAR** e, em seguida, **Bancos de Dados SQL**.
3. Navegue até o banco de dados e selecione-o.
4. Na parte superior da folha do banco de dados, selecione **Restaurar**.
5. Especifique um nome de banco de dados, um determinado momento e, em seguida, clique em **Criar**.
6. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**, no lado esquerdo da tela.

Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado para ser usado de acordo com o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).
###PowerShell
Use o PowerShell para executar a restauração do banco de dados de modo programático.

> [AZURE.IMPORTANT]Este artigo contém comandos para versões do Azure PowerShell, *exceto* as versões 1.0 e posteriores. Você pode verificar sua versão do Azure PowerShell com o comando **Get-Module azure | format-table version**.

Para restaurar um banco de dados com a Restauração Pontual, use o cmdlet [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Para ver um passo a passo detalhado, confira nosso [vídeo explicativo](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/).

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado para ser usado de acordo com o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).

###API REST 
Use a API REST para executar a restauração do banco de dados de modo programático.

1. Obtenha o banco de dados que deseja restaurar usando a operação [Obter banco de dados](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Crie a solicitação de restauração usando a operação [Criar solicitação de restauração de banco de dados](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Acompanhe a solicitação de restauração usando a operação [Status de operações do banco de dados](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado para ser usado de acordo com o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).

##Recuperar um banco de dados excluído
No caso de um banco de dados excluído, o Banco de Dados SQL do Azure permite restaurar o banco de dados excluído até o momento da exclusão. O Banco de Dados SQL do Azure armazena o backup do banco de dados excluído pelo período de retenção do banco de dados.

O período de retenção de um banco de dados excluído é determinado pela camada de serviço do banco de dados enquanto ela existiu ou pelo número de dias em que o banco de dados existe, o que for menor. Para saber mais sobre retenção de banco de dados, leia nossa [visão geral da continuidade dos negócios](sql-database-business-continuity.md).

> [AZURE.NOTE]A restauração de um banco de dados cria um novo banco de dados. É importante garantir que o servidor que você está restaurando tenha capacidade de DTU suficiente para o novo banco de dados. Você pode solicitar um aumento dessa cota [entrando em contato com o suporte](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portal do Azure
1. Faça logon no [Portal do Azure](https://portal.Azure.com)
2. No lado esquerdo da tela, selecione **PROCURAR** e, em seguida, **Servidores SQL**.
3. Navegue até o servidor e selecione-o.
4. Em **Operações** na folha do servidor, selecione **Bancos de Dados Excluídos**.
5. Selecione o banco de dados excluído que deseja restaurar.
6. Especifique um nome de banco de dados e clique em **Criar**.
7. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**, no lado esquerdo da tela.

Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado para ser usado de acordo com o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).

###PowerShell
Use o PowerShell para executar a restauração do banco de dados de modo programático.

Para restaurar um banco de dados excluído, use o cmdlet [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Para ver um passo a passo detalhado, confira nosso [vídeo explicativo](http://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/).

1. Localize o banco de dados excluído e a data da exclusão na lista de bancos de dados excluídos.
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. Obtenha o banco de dados excluído e inicie a restauração.

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado para ser usado de acordo com o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).

###API REST 
Use a API REST para executar a restauração do banco de dados de modo programático.

1.	Liste todos os bancos de dados excluídos restauráveis usando a operação [Listar bancos de dados descartados restauráveis](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Obtenha os detalhes do banco de dados excluído que deseja restaurar usando a operação [Obter banco de dados descartados restauráveis](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado para ser usado de acordo com o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).
 

<!---HONumber=Oct15_HO3-->