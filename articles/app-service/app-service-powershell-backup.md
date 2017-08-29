---
title: "Usar o PowerShell para fazer backup e restaurar aplicativos do Serviço de Aplicativo"
description: "Saiba como usar o PowerShell para fazer backup e restaurar um aplicativo no Serviço de Aplicativo do Azure"
services: app-service
documentationcenter: 
author: NKing92
manager: erikre
editor: 
ms.assetid: 7ea8661e-aefb-4823-9626-6bff980cdebf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: nicking
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 34a7e1d025c301ca056753d964bb3c5f4f1a62d8
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>Usar o PowerShell para fazer backup e restaurar aplicativos do Serviço de Aplicativo
> [!div class="op_single_selector"]
> * [PowerShell](app-service-powershell-backup.md)
> * [API REST](../app-service-web/websites-csm-backup.md)
> 
> 

Saiba como usar o Azure PowerShell para fazer backup e restaurar [aplicativos do Serviço de Aplicativo](https://azure.microsoft.com/services/app-service/web/). Para saber mais sobre os backups de aplicativo Web, incluindo requisitos e restrições, confira [Fazer backup de um aplicativo Web no Serviço de Aplicativo do Azure](../app-service-web/web-sites-backup.md).

## <a name="prerequisites"></a>Pré-requisitos
Para usar o PowerShell para gerenciar os backups do seu aplicativo, você precisa do seguinte:

* **Um URL de SAS** que permite o acesso de leitura e gravação a um contêiner do Armazenamento do Azure. Consulte [Noções básicas do modelo de SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) para ver uma explicação sobre URLs SAS. Consulte [Usar o Azure PowerShell com o Armazenamento do Azure](../storage/common/storage-powershell-guide-full.md) para obter exemplos de gerenciamento do Armazenamento do Azure usando o PowerShell.
* **Uma cadeia de conexão de banco de dados** se você quiser fazer backup de um banco de dados junto com seu aplicativo Web.

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>Como gerar uma URL SAS para usar com os cmdlets do backup do aplicativo Web
Uma URL SAS pode ser gerada com o PowerShell. Aqui está um exemplo de como gerar uma que pode ser usada com os cmdlets discutidos neste artigo.

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>Instalar o Azure PowerShell 1.3.2 ou superior
Confira [Como usar o Azure PowerShell com o Azure Resource Manager](/powershell/azure/overview) para obter instruções sobre como instalar e usar o Azure PowerShell.

## <a name="create-a-backup"></a>Criar um backup
Use o cmdlet New-AzureRmWebAppBackup para criar um backup de um aplicativo Web.

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Isso cria um backup com um nome gerado automaticamente. Se você quiser fornecer um nome para seu backup, use o parâmetro opcional BackupName.

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Para incluir um banco de dados no backup, primeiro crie uma configuração de backup de banco de dados usando o cmdlet New-AzureRmWebAppDatabaseBackupSetting e depois forneça essa configuração no parâmetro Databases do cmdlet New-AzureRmWebAppBackup. O parâmetro Databases aceita uma matriz de configurações de banco de dados, permitindo que você faça backup de mais de um banco de dados.

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>Obter backups
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

## <a name="schedule-automatic-backups"></a>Agendar backups automáticos
Você pode agendar backups automáticos em um intervalo especificado. Para configurar um agendamento de backup, use o cmdlet Edit-AzureRmWebAppBackupConfiguration. Este cmdlet aceita vários parâmetros:

* **Name** - O nome do aplicativo Web.
* **ResourceGroupName** - O nome do grupo de recursos que contém o aplicativo Web.
* **Slot** - Opcional. O nome do slot do aplicativo Web.
* **StorageAccountUrl** - A URL SAS do contêiner do Armazenamento do Azure usado para armazenar os backups.
* **FrequencyInterval** - Valor numérico relacionado à frequência dos backups. Deve ser um número inteiro positivo.
* **FrequencyUnit** - Unidade de tempo relacionada à frequência dos backups. As opções são Hour (Hora) e Day (Dia).
* **RetentionPeriodInDays** - Quantos dias os backups automáticos devem ser salvos antes de serem automaticamente excluídos.
* **StartTime** - Opcional. A hora de início dos backups automáticos. Os backups começarão imediatamente se esse parâmetro for nulo. Ele deve ser um DateTime.
* **Databases** - Opcional. Uma matriz de DatabaseBackupSettings para backup dos bancos de dados.
* **KeepAtLeastOneBackup** - Parâmetro opcional comutado. Forneça esse parâmetro se um backup sempre precisar ser mantido na conta de armazenamento, independentemente de sua idade.

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

## <a name="restore-a-web-app-from-a-backup"></a>Restaurar um aplicativo Web a partir de um backup
Para restaurar um aplicativo Web a partir de um backup, use o cmdlet Restore-AzureRmWebAppBackup. A maneira mais fácil de usar este cmdlet é direcionar um objeto de backup recuperado do cmdlet Get-AzureRmWebAppBackup ou do cmdlet Get-AzureRmWebAppBackupList.

Assim que você tiver um objeto de backup, será possível direcioná-lo para o cmdlet Restore-AzureRmWebAppBackup. Especifique o parâmetro de opção Overwrite para indicar que pretende substituir o conteúdo de seu aplicativo Web pelo conteúdo do backup. Se o backup contiver bancos de dados, eles também serão restaurados.

        $backup | Restore-AzureRmWebAppBackup -Overwrite

Veja a seguir um exemplo de como usar Restore-AzureRmWebAppBackup especificando todos os parâmetros.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>Excluir um conjunto de backups
Para excluir um backup, use o cmdlet Remove-AzureRmWebAppBackup. Isso removerá o backup da sua conta de armazenamento. Especifique o nome do aplicativo, seu grupo de recursos e a ID do backup que você quer excluir.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

Também é possível direcionar um objeto de backup para o cmdlet Remove-AzureRmWebAppBackup para excluí-lo.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite

