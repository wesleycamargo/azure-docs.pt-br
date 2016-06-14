<properties
	pageTitle="Usar o PowerShell para fazer backup e restaurar aplicativos do Serviço de Aplicativo"
	description="Saiba como usar o PowerShell para fazer backup e restaurar um aplicativo no Serviço de Aplicativo do Azure"
	services="app-service"
	documentationCenter=""
	authors="NKing92"
	manager="wpickett"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="nicking"/>
# Usar o PowerShell para fazer backup e restaurar aplicativos do Serviço de Aplicativo

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [API REST](../app-service-web/websites-csm-backup.md)

Saiba como usar o Azure PowerShell para fazer backup e restaurar [aplicativos do Serviço de Aplicativo](https://azure.microsoft.com/services/app-service/web/). Para saber mais sobre os backups de aplicativo Web, incluindo requisitos e restrições, confira [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](../app-service-web/web-sites-backup.md).

## Pré-requisitos
Para usar o PowerShell para gerenciar os backups de seu aplicativo, você precisará do seguinte:

- **Um URL de SAS** que permite o acesso de leitura e gravação a um contêiner do Armazenamento do Azure. Confira [Noções básicas do modelo de SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) para saber mais sobre URLs de SAS.
- **Uma cadeia de conexão de banco de dados** se você quiser fazer backup de um banco de dados junto com seu aplicativo Web.

##Instalar o Azure PowerShell 1.3.2 ou superior

Confira [Como usar o Azure PowerShell com o Azure Resource Manager](../powershell-install-configure.md) para obter instruções sobre como instalar e usar o Azure PowerShell.

## Criar um backup

Use o cmdlet New-AzureRmWebAppBackup para criar um backup de um aplicativo Web.

		$sasUrl = "<your SAS URL>"
		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Isso criará um backup com um nome gerado automaticamente. Se você quiser fornecer um nome para seu backup, use o parâmetro opcional BackupName.

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Se você quiser incluir um banco de dados como parte de seu backup, crie uma configuração de backup de banco de dados usando o cmdlet New-AzureRmWebAppDatabaseBackupSetting e, depois, forneça essa configuração no parâmetro Databases do cmdlet New-AzureRmWebAppBackup. O parâmetro Databases aceita uma matriz de configurações de banco de dados, permitindo que você faça backup de mais de um banco de dados.

		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## Obter backups

O cmdlet Get-AzureRmWebAppBackupList retornará uma matriz de todos os backups de um aplicativo Web. Você deve fornecer o nome do aplicativo Web e de seu grupo de recursos.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Para obter um backup específico, use o cmdlet Get-AzureRmWebAppBackup.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

Você também pode redirecionar um objeto de aplicativo Web para qualquer um dos cmdlets do gerenciamento de backup para sua conveniência.

		$app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
		$backupList = $app | Get-AzureRmWebAppBackupList
		$backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## Agendar backups automáticos

Você pode agendar backups automáticos em um intervalo especificado. Para configurar um agendamento de backup, use o cmdlet Edit-AzureRmWebAppBackupConfiguration. Este cmdlet aceita vários parâmetros:

- **Name** - O nome do aplicativo Web.
- **ResourceGroupName** - O nome do grupo de recursos que contém o aplicativo Web.
- **Slot** - Opcional. O nome do slot do aplicativo Web.
- **StorageAccountUrl** - A URL SAS do contêiner do Armazenamento do Azure usado para armazenar os backups.
- **FrequencyInterval** - Valor numérico relacionado à frequência dos backups. Deve ser um número inteiro positivo.
- **FrequencyUnit** - Unidade de tempo relacionada à frequência dos backups. As opções são Hour (Hora) e Day (Dia).
- **RetentionPeriodInDays** - Quantos dias os backups automáticos devem ser salvos antes de serem automaticamente excluídos.
- **StartTime** - Opcional. A hora de início dos backups automáticos. Os backups começarão imediatamente se esse parâmetro for nulo. Ele deve ser um DateTime.
- **Databases** - Opcional. Uma matriz de DatabaseBackupSettings para backup dos bancos de dados.
- **KeepAtLeastOneBackup** - Parâmetro opcional comutado. Forneça esse parâmetro se um backup sempre precisar ser mantido na conta de armazenamento, independentemente de sua idade.

Veja a seguir um exemplo de como usar este cmdlet.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
		  -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
		  -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Para obter a agenda de backup atual, use o cmdlet Get-AzureRmWebAppBackupConfiguration. Isso pode ser útil para modificar uma agenda que já foi configurada.

		$configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

		# Modify the configuration slightly
		$configuration.FrequencyInterval = 2
		$configuration.FrequencyUnit = "Day"

		# Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
		$configuration | Edit-AzureRmWebAppBackupConfiguration

## Restaurar um aplicativo Web a partir de um backup

Para restaurar um aplicativo Web a partir de um backup, use o cmdlet Restore-AzureRmWebAppBackup. A maneira mais fácil de usar este cmdlet é direcionar um objeto de backup recuperado do cmdlet Get-AzureRmWebAppBackup ou do cmdlet Get-AzureRmWebAppBackupList.

Assim que você tiver um objeto de backup, será possível direcioná-lo para o cmdlet Restore-AzureRmWebAppBackup. Você deve especificar o parâmetro de opção Overwrite para indicar que pretende substituir o conteúdo de seu aplicativo Web pelo conteúdo do backup. Se o backup contiver bancos de dados, esses bancos de dados também serão restaurados.

		$backup | Restore-AzureRmWebAppBackup -Overwrite

Veja abaixo um exemplo de como usar Restore-AzureRmWebAppBackup especificando todos os parâmetros.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$blobName = "ContosoBackup.zip"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## Excluir um conjunto de backups

Para excluir um backup, use o cmdlet Remove-AzureRmWebAppBackup. Isso removerá o backup de sua conta de armazenamento. Você deve especificar o nome do aplicativo, seu grupo de recurso e a ID do backup que você quer excluir.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

Também é possível direcionar um objeto de backup para o cmdlet Remove-AzureRmWebAppBackup para excluí-lo.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
		$backup | Remove-AzureRmWebAppBackup -Overwrite

<!---HONumber=AcomDC_0601_2016-->