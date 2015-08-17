<properties 
   pageTitle="Restaurar um banco de dados do SQL Azure excluído no PowerShell do Azure" 
   description="Banco de dados SQL do Microsoft Azure, restaurar banco de dados excluído, recuperar banco de dados excluído, PowerShell do Azure" 
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
   ms.date="07/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Restaurar um banco de dados do SQL Azure excluído no PowerShell do Azure

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## Visão geral

Este tutorial mostra como restaurar um banco de dados do SQL Azure excluído no [PowerShell do Azure](../powershell-install-configure.md). Você pode restaurar um banco de dados que foi excluído durante seu período de retenção para o ponto em que ele foi excluído. O período de retenção é determinado pela camada de serviço do banco de dados.

Restaurar um banco de dados do SQL Azure excluído cria um novo banco de dados. O serviço seleciona automaticamente a camada de serviço com base no backup usado no ponto de restauração. Certifique-se de ter cota disponível no servidor lógico para criar outro banco de dados. Se desejar solicitar uma cota maior, entre em contato com [Suporte do Azure](http://azure.microsoft.com/support/options/).

## Segurança e restrições

Consulte [Restaurar um banco de dados do SQL Azure excluído no portal do Azure](sql-database-restore-deleted-database-tutorial-management-portal.md).

## Como: Restaurar um banco de dados do SQL Azure excluído no PowerShell do Azure

> [AZURE.VIDEO restore-a-deleted-sql-database-with-microsoft-azure-powershell]

Você deve usar a autenticação baseada em certificado para executar os seguintes cmdlets. Para obter mais informações, consulte a seção *Usar o método de certificado* em [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md#use-the-certificate-method).

1. Obtenha a lista de bancos de dados recuperáveis usando o cmdlet [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx).
	* Use a opção **RestorableDropped** e especifique o **ServerName** do servidor do qual o banco de dados foi excluído.
	* Executar o comando a seguir armazena os resultados em uma variável chamada **$RecoverableDBs**.
	
	`PS C:\>$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" –RestorableDropped`

2. Escolha o banco de dados excluído que você deseja restaurar na lista de bancos de dados excluídos.

	* Digite o número de banco de dados excluído da lista **$RecoverableDBs**.  

	`PS C:\>$Database = $RecoverableDBs[<deleted database number>]`

	* Para obter mais informações sobre como obter um objeto de banco de dados descartado restaurável, consulte [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx).

3. Comece a restauração usando o cmdlet [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Especifique os seguintes parâmetros:
	* **SourceRestorableDroppedDatabase**
	* **TargetDatabaseName** do banco de dados que você está restaurando.

	Armazenar o que é retornado para uma variável denominada **$RestoreRequest**. Essa variável contém a ID da solicitação de restauração que é usada para monitorar o status de uma restauração.
	
	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “myrestoredDB”`

Uma restauração pode levar algum tempo para concluir. Para monitorar o status da restauração, use o cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) e especifique os seguintes parâmetros:

* **ServerName** do banco de dados que você está restaurando.
* **OperationGuid** que é a ID de solicitação de restauração que foi armazenada na variável **$RestoreRequest** na etapa 3.

	`PS C:\>Get-AzureSqlDatabaseOperation –ServerName "myserver" –OperationGuid $RestoreRequest.RequestID`

Os campos **Estado** e **PercentComplete** mostram o status da restauração.

## Próximas etapas

Para obter mais informações, consulte o seguinte:

[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Restauração e Backup de banco de dados do SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[PowerShell do Azure](http://msdn.microsoft.com/library/azure/jj156055.aspx)

<!---HONumber=August15_HO6-->