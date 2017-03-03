---
title: 'PowerShell: Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC | Microsoft Docs'
description: Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: 162147607baa36de0487cebc06e7ada20f3dd0c0
ms.lasthandoff: 02/11/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-to-a-bacpac-file-by-using-powershell"></a>Exportar um Banco de Dados SQL do Azure ou um SQL Server para um arquivo BACPAC usando o PowerShell

Este artigo fornece instruções para arquivar seu Banco de Dados SQL do Azure ou um SQL Server para um arquivo BACPAC (armazenado no armazenamento de Blobs do Azure) usando o PowerShell. Para obter uma visão geral de como exportar para um arquivo BACPAC, confira [Exportar para um BACPAC](sql-database-export.md).

> [!NOTE]
> Você também pode exportar o arquivo de banco de dados SQL do Azure para um arquivo BACPAC usando o [Portal do Azure](sql-database-export-portal.md), [SQL Server Management Studio](sql-database-export-ssms.md) ou o [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Uma assinatura do Azure.
* Um banco de dados SQL do Azure.
* Uma [conta de Armazenamento Standard do Azure](../storage/storage-create-storage-account.md)com um contêiner de blob para armazenar o BACPAC no armazenamento standard.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>Exportar o banco de dados
O cmdlet [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx) envia ao serviço uma solicitação para exportar banco de dados. Dependendo do tamanho do banco de dados, a operação de exportação poderá demorar para ser concluída.

> [!IMPORTANT]
> Para garantir um arquivo BACPAC com consistência transacional, primeiro você deve [criar uma cópia do banco de dados](sql-database-copy-powershell.md)e, em seguida, exportar essa cópia.
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorar o progresso da operação de exportação
Depois de executar [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx), você poderá verificar o status da solicitação executando [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx). Executar isso imediatamente após a solicitação geralmente retorna **Status : InProgress**. Quando você vir **Status: Êxito**, a exportação estará concluída.

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Exemplo de exportação de banco de dados SQL
O exemplo a seguir exporta o banco de dados SQL existente para um BACPAC e, em seguida, mostra como verificar o status da operação de exportação.

Para executar o exemplo, há algumas variáveis em que você precisará substituir pelos valores específicos do seu banco de dados e conta de armazenamento. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento para obter o nome da conta de armazenamento, nome do contêiner de blob e valor de chave. Você pode encontrar a chave clicando em **Chaves de acesso** na sua folha de conta de armazenamento.

Substitua o `VARIABLE-VALUES` a seguir por valores para os recursos do Azure específicos. O nome do banco de dados é o banco de dados existente que você deseja exportar.

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
    $creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink

## <a name="automate-export-using-azure-automation"></a>Automatizar a exportação usando a Automação do Azure

A Exportação Automatizada do Banco de Dados SQL do Azure está agora em visualização e será desativada em 1º de março de 2017. A partir de 1 de dezembro de 2016, não será mais possível configurar a exportação automatizada em qualquer Banco de Dados SQL. Todos os trabalhos de exportação automatizada existentes continuarão funcionando até 1º de março de 2017. Após 1º de dezembro de 2016, você poderá usar a [retenção de backup de longo prazo](sql-database-long-term-retention.md) ou a [Automação do Azure](../automation/automation-intro.md) para arquivar bancos de dados SQL periodicamente, usando o PowerShell periodicamente de acordo com um agendamento de sua escolha. Para obter um script de exemplo, baixe o [script de exemplo no GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 


## <a name="next-steps"></a>Próximas etapas
* Para saber como importar um Banco de Dados SQL do Azure usando o Powershell, consulte [Importar um BACPAC usando o PowerShell](sql-database-import-powershell.md).
* Para saber mais sobre como importar um BACPAC usando o SQLPackage, confira [Importar um BACPAC para um Banco de Dados SQL do Azure usando SqlPackage](sql-database-import-sqlpackage.md)
* Para saber mais sobre como importar um BACPAC usando o Portal do Azure, confira [Importar um BACPAC para um Banco de Dados SQL do Azure usando o Portal do Azure](sql-database-import-portal.md)
* Para ver uma discussão de todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).
* Para saber mais sobre retenção de backup de longo prazo de um backup do Banco de Dados SQL do Azure como uma alternativa para a exportação de um banco de dados para fins de arquivamento, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md)
* Para saber mais sobre como importar um BACPAC para um banco de dados do SQL Server, confira [Importar um BACPAC para um banco de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



## <a name="additional-resources"></a>Recursos adicionais
* [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx)


