---
title: Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell
description: Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 08/15/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: c886e8e61b00de2f07d7f5a98c2d2f4d5b29b7cf
ms.openlocfilehash: dd264dfc73962a575f0d4b1a32a9ec02752c33ba


---
# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

Este artigo fornece instruções para arquivar seu banco de dados SQL do Azure para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) armazenado no armazenamento de blobs do Azure usando o PowerShell.

Quando você precisa criar um arquivo morto de um Banco de Dados SQL do Azure, pode exportar o esquema de banco de dados e os dados para um arquivo BACPAC. Um arquivo BACPAC é simplesmente um arquivo ZIP com uma extensão de .bacpac. Um arquivo BACPAC pode posteriormente ser armazenado no Armazenamento de Blobs ou no armazenamento local em uma localização local. Ele também pode ser importado novamente no banco de dados SQL ou em uma instalação do SQL Server local.

## <a name="considerations"></a>Considerações

* Para um arquivo morto ser transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação, ou então que você esteja exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu Banco de Dados SQL do Azure.
* O tamanho máximo de um arquivo BACPAC arquivado no Armazenamento de Blobs do Azure é de 200 GB. Para arquivar um arquivo BACPAC maior no armazenamento local, use o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Esse utilitário é fornecido com o Visual Studio e o SQL Server. Você também pode [baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente do SQL Server Data Tools para obter esse utilitário.
* Não há suporte para o arquivamento no armazenamento premium do Azure usando um arquivo BACPAC.
* Se a operação de exportação exceder 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
  * Aumentar temporariamente seu nível de serviço.
  * Interromper toda a atividade de leitura e gravação durante a exportação.
  * Use um [índice clusterizado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir a verificação da tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW_STATISTICS** e verificar se *RANGE_HI_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, consulte [Backups automáticos do Banco de Dados SQL](sql-database-automated-backups.md).
> 
> 

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

     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorar o progresso da operação de exportação
Depois de executar [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx), você poderá verificar o status da solicitação executando [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx). Executar isso imediatamente após a solicitação geralmente retorna **Status : InProgress**. Quando você vir **Status : Succeeded** , a exportação estará concluída.

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
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink

## <a name="automate-export-using-azure-automation"></a>Automatizar a exportação usando a Automação do Azure

Você pode usar a automação do Azure para arquivar Bancos de Dados SQL periodicamente de acordo com uma programação de sua escolha. Você pode baixar o [script de exemplo no GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). Consulte [Visão geral da automação do Azure](../automation/automation-intro.md) para obter mais detalhes sobre a Automação do Azure.

## <a name="next-steps"></a>Próximas etapas
* Para saber como importar um Banco de Dados SQL do Azure usando o Powershell, consulte [Importar um BACPAC usando o PowerShell](sql-database-import-powershell.md).

## <a name="additional-resources"></a>Recursos adicionais
* [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx)




<!--HONumber=Nov16_HO3-->


