---
title: Exportar um banco de dados SQL do Azure para um arquivo BACPAC | Microsoft Docs
description: Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o Portal do Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: move data
ms.devlang: NA
ms.date: 04/05/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: eadd300dcda2f160589c5e8e4fb7508445ef9944
ms.lasthandoff: 04/10/2017


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC

Este artigo aborda como exportar um Banco de Dados SQL do Azure para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Este artigo discute o uso dos seguintes métodos:
- O [Portal do Azure](https://portal.azure.com)
- o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)
- o cmdlet [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)
- o assistente [Exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) no [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).

> [!IMPORTANT] 
> A Exportação Automatizada do Banco de Dados SQL do Azure está agora em visualização e será desativada em 1º de março de 2017. A partir de 1 de dezembro de 2016, não será mais possível configurar a exportação automatizada em qualquer Banco de Dados SQL. Todos os trabalhos de exportação automatizada existentes continuarão funcionando até 1º de março de 2017. Após 1º de dezembro de 2016, você poderá usar a [retenção de backup de longo prazo](sql-database-long-term-retention.md
) ou a [Automação do Azure](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) para arquivar bancos de dados SQL periodicamente, usando o PowerShell periodicamente de acordo com um agendamento de sua escolha. Para obter um script de exemplo, baixe o [script PowerShell de exemplo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) do GitHub.
>

> [!IMPORTANT] 
> A Exportação Automatizada do Banco de Dados SQL do Azure está agora em visualização e será desativada em 1º de março de 2017. A partir de 1 de dezembro de 2016, não será mais possível configurar a exportação automatizada em qualquer Banco de Dados SQL. Todos os trabalhos de exportação automatizada existentes continuarão funcionando até 1º de março de 2017. Após 1º de dezembro de 2016, você poderá usar a [retenção de backup de longo prazo](sql-database-long-term-retention.md
) ou a [Automação do Azure](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) para arquivar bancos de dados SQL periodicamente, usando o PowerShell periodicamente de acordo com um agendamento de sua escolha. Para obter um script de exemplo, baixe o [script PowerShell de exemplo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) do GitHub.
>

## <a name="overview"></a>Visão geral

Quando for preciso exportar um banco de dados para arquivamento ou para mover para outra plataforma, você pode exportar os dados e o esquema do banco de dados para um arquivo BACPAC. Um arquivo BACPAC é um arquivo ZIP com uma extensão BACPAC que contém os metadados e dados de um banco de dados do SQL Server. Um arquivo BACPAC pode ser colocado no Armazenamento de Blobs do Azure ou em uma determinada localização no armazenamento local e depois importado novamente para o Banco de Dados SQL do Azure ou uma instalação local do SQL Server. 

> [!IMPORTANT]
> Se você estiver exportando do SQL Server como um prelúdio para a migração para o Banco de Dados SQL do Azure, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).
> 

## <a name="considerations"></a>Considerações

* Para que uma exportação seja transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou que você esteja exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu banco de dados SQL do Azure.
* Se você estiver exportando para o armazenamento de blobs, o tamanho máximo de um arquivo BACPAC é de 200 GB. Para arquivar um arquivo BACPAC maior, exporte para o armazenamento local.
* Não há suporte para a exportação de um arquivo BACPAC no armazenamento Premium do Azure usando os métodos abordados neste artigo.
* Se a operação de exportação do Banco de Dados SQL do Azure exceder 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
  * Aumentar temporariamente seu nível de serviço.
  * Interromper toda a atividade de leitura e gravação durante a exportação.
  * Use um [índice clusterizado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir a verificação da tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW_STATISTICS** e verificar se *RANGE_HI_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md) e [Backups do Banco de Dados SQL](sql-database-automated-backups.md).  
> 

## <a name="azure-portal"></a>Portal do Azure

Para exportar um banco de dados usando o Portal do Azure, abra a página do banco de dados e clique em **Exportar** na barra de ferramentas. Especifique o nome do arquivo *.bacpac, forneça a conta de Armazenamento do Azure e o contêiner para a exportação e forneça as credenciais para conectar-se ao banco de dados de origem.  

   ![Exportação de banco de dados](./media/sql-database-export/database-export.png)

Para monitorar o progresso da operação de exportação, abra a página para o servidor lógico que contém o banco de dados que está sendo exportado. Role para baixo até **Operações** e, em seguida, clique em **Importar/Exportar histórico**.

## <a name="sqlpackage-utility"></a>Utilitário SQLPackage

Para exportar um Banco de Dados SQL usando o utilitário de linha de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx), consulte [Exportar parâmetros e propriedades](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). O utilitário SQLPackage acompanha as últimas versões do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) ou você pode baixar a última versão do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente no Centro de Download da Microsoft.

Recomendamos o uso do utilitário SQLPackage para escala e desempenho na maioria dos ambientes de produção. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados do Azure SQL usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

## <a name="sql-server-management-studio"></a>SQL Server Management Studio

As versões mais recentes do SQL Server Management Studio também fornecem um Assistente para exportar um Banco de Dados SQL do Azure para um arquivo bacpac. Consulte [Exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

Use o cmdlet [New-AzureRmSqlDatabaseImport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) para enviar uma solicitação de exportação de banco de dados para o serviço de Banco de Dados SQL do Azure. Dependendo do tamanho do banco de dados, a operação de exportação poderá demorar para ser concluída.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Para verificar o status da solicitação de exportação, use o cmdlet [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus). Executar isso imediatamente após a solicitação geralmente retorna **Status: InProgress**. Quando você vir **Status: Êxito**, a exportação estará concluída.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

### <a name="export-sql-database-example"></a>Exemplo de exportação de banco de dados SQL
O exemplo a seguir exporta o Banco de Dados SQL do Azure existente para um BACPAC e, em seguida, mostra como verificar o status da operação de exportação.

Para executar o exemplo, há algumas variáveis em que você precisará substituir pelos valores específicos do seu banco de dados e conta de armazenamento. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento para obter o nome da conta de armazenamento, nome do contêiner de blob e valor de chave. Você pode encontrar a chave clicando em **Chaves de acesso** na sua folha de conta de armazenamento.

Substitua o `VARIABLE-VALUES` a seguir por valores para os recursos do Azure específicos. O nome do banco de dados é o banco de dados existente que você deseja exportar.

```powershell
$subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId

# Database to export
$DatabaseName = "DATABASE-NAME"
$ResourceGroupName = "RESOURCE-GROUP-NAME"
$ServerName = "SERVER-NAME
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
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre retenção de backup de longo prazo de um backup do Banco de Dados SQL do Azure como uma alternativa para a exportação de um banco de dados para fins de arquivamento, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados do Azure SQL usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Para saber mais sobre como importar um BACPAC para um Banco de Dados do SQL Server, confira [Importar um BACPAC para um banco de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
* Para saber mais sobre como exportar um BACPAC de um banco de dados do SQL Server, consulte [Exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) e [Migrar seu primeiro banco de dados](sql-database-migrate-your-sql-server-database.md).

