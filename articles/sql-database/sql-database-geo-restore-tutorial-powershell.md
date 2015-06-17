<properties 
   pageTitle="Recuperar um banco de dados do SQL Azure usando a restauração geográfica no PowerShell do Azure" 
   description="Restauração geográfica, Banco de dados SQL do Microsoft Azure, banco de dados de restauração, banco de dados de recuperação, PowerShell do Azure" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Recuperar um banco de dados do SQL Azure usando a restauração geográfica no PowerShell do Azure

> [AZURE.SELECTOR]
- [Restauração geográfica - portal](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)
- [Restauração geográfica - API REST](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-rest/)

## Visão geral

Este tutorial mostra como recuperar um banco de dados do SQL Azure usando a restauração geográfica no[PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). A restauração geográfica é a proteção de recuperação de desastres do núcleo incluída para camadas de serviço do banco de dados do SQL Azure Basic, Standard e Premium.

## Segurança e restrições

Consulte [Recuperar um banco de dados do SQL Azure usando a restauração geográfica no portal do Azure](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/).

## Como: Recuperar um banco de dados do SQL Azure com a restauração geográfica no PowerShell do Azure

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Geo-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Você deve usar a autenticação baseada em certificado para executar os seguintes cmdlets. Para obter mais informações, consulte a seção *Usar o método de certificado* em[Como instalar e configurar o PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Obter a lista de bancos de dados recuperáveis usando o cmdlet [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Especifique os seguintes parâmetros:
	* **ServerName** onde o banco de dados está localizado.	

	`PS C:>Get-AzureSqlRecoverableDatabase -ServerName "myserver"`

2. Escolha o banco de dados que você deseja recuperar usando o cmdlet [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Especifique os seguintes parâmetros:
	* **ServerName** onde o banco de dados está localizado.
	* **DatabaseName** do banco de dados que você está recuperando.

	`PS C:>$Database = Get-AzureSqlRecoverableDatabase -ServerName "myserver" -DatabaseName "mydb"`
	 
3. Comece a recuperação usando o cmdlet [Start-AzureSqlDatabaseRecovery](http://msdn.microsoft.com/library/dn720224.aspx). Especifique os seguintes parâmetros:	
	* **SourceDatabase** que você deseja recuperar.
	* **TargetDatabaseName** do banco de dados que você está fazendo a recuperação.
	* **TargetServerName** que você deseja recuperar do banco de dados.

	Armazenar o que é retornado para uma variável denominada **$RestoreRequest**. Essa variável contém a ID da solicitação de restauração que é usada para monitorar o status de uma restauração.

	`PS C:>$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database -TargetDatabaseName "myrecoveredDB" -TargetServerName "mytargetserver"`
	
Uma recuperação de banco de dados pode levar algum tempo para concluir. Para monitorar o status da recuperação, use o cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) e especifique os seguintes parâmetros:

* **ServerName** do banco de dados que você está restaurando.
* **OperationGuid** que é a ID de solicitação de restauração que foi armazenada na variável **$RecoveryRequest** na etapa 3.

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "mytargetserver" -OperationGuid $RecoveryRequest.ID`

Os campos **Estado** e **PercentComplete** mostram o status da restauração.

## Próximas etapas

Para obter mais informações, consulte o seguinte:  

[Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no portal do Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/).

[Restaurar um banco de dados do SQL Azure excluído no portal do Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Restauração e Backup de banco de dados do SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauração geográfica do banco de dados do SQL Azure (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
 