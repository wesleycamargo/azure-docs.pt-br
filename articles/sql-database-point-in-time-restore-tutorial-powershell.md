<properties 
   pageTitle="Restaurar um banco de dados do Azure SQL usando o ponto de restauração pontual no Azure PowerShell" 
   description="Ponto de restauração pontual, Banco de dados SQL do Microsoft Azure, banco de dados de restauração, banco de dados de recuperação, PowerShell do Azure" 
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

# Restaurar um banco de dados do Azure SQL usando o ponto de restauração pontual no Azure PowerShell

> [AZURE.SELECTOR]
- [Ponto de restauração pontual - portal](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [Restauração pontual - API REST](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-rest/)

## Visão geral

Este tutorial mostra como restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no [PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Banco de dados do SQL Azure tem backups internos para dar suporte a ponto de autoatendimento na restauração pontual para camadas de serviço Basic, Standard e Premium.

A restauração pontual cria um novo banco de dados. O serviço seleciona automaticamente a camada de serviço com base no backup usado no ponto de restauração. Certifique-se de ter cota disponível no servidor lógico para criar outro banco de dados. Se desejar solicitar uma cota maior, entre em contato com [Suporte do Azure](http://azure.microsoft.com/support/options/).

## Segurança e restrições

Consulte [Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no portal do Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/).

## Como: Restaurar um banco de dados do Azure SQL usando o ponto de restauração pontual no Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Point-in-Time-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Você deve usar a autenticação baseada em certificado para executar os seguintes cmdlets. Para obter mais informações, consulte a seção *Use the certificate method* em[Como instalar e configurar o PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Obtenha o banco de dados que você deseja restaurar usando o cmdlet [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx). Especifique os seguintes parâmetros:
	* **ServerName** onde o banco de dados está localizado.
	* **DatabaseName** do banco de dados que você deseja restaurar.	

	`PS C:\>$Database = Get-AzureSqlDatabase -ServerName "myserver" -DatabaseName "mydb"`

2. Comece a restauração usando o cmdlet [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Especifique os seguintes parâmetros:	
	* **SourceDatabase** que você deseja fazer a restauração.
	* **TargetDatabaseName** do banco de dados que você está restaurando.
	* **PointInTime** que você deseja restaurar.

	Armazenar o que é retornado para uma variável denominada **$RestoreRequest**. Essa variável contém a ID da solicitação de restauração que é usada para monitorar o status de uma restauração. 

	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database -TargetDatabaseName "myrestoredDB" -PointInTime "2015-01-01 06:00:00"`

Uma restauração pode levar algum tempo para concluir. Para monitorar o status da restauração, use o cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) e especifique os seguintes parâmetros:

* **ServerName** do banco de dados que você está restaurando.
* **OperationGuid** que é a ID de solicitação de restauração que foi armazenada na variável **$RestoreRequest** na etapa 2.

	`PS C:\>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

Os campos **Estado** e **PercentComplete** mostram o status da restauração. 

## Próximas etapas

Para obter mais informações, consulte o seguinte:  

[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Restauração e Backup de banco de dados do SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Ponto de banco de dados do SQL Azure em tempo de restauração (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
