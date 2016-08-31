<properties
    pageTitle="Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell"
    description="Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/15/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Este artigo fornece instruções para arquivar seu banco de dados SQL do Azure para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) armazenado no armazenamento de blobs do Azure usando o PowerShell.

Quando você precisa criar um arquivo morto de um Banco de Dados SQL do Azure, pode exportar o esquema de banco de dados e os dados para um arquivo BACPAC. Um arquivo BACPAC é simplesmente um arquivo ZIP com uma extensão de .bacpac. Um arquivo BACPAC pode posteriormente ser armazenado no Armazenamento de Blobs ou no armazenamento local em uma localização local. Ele também pode ser importado novamente no banco de dados SQL ou em uma instalação do SQL Server local.

**Considerações**

- Para um arquivo morto ser transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação, ou então que você esteja exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu Banco de Dados SQL do Azure.
- O tamanho máximo de um arquivo BACPAC arquivado no Armazenamento de Blobs do Azure é de 200 GB. Para arquivar um arquivo BACPAC maior no armazenamento local, use o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esse utilitário é fornecido com o Visual Studio e o SQL Server. Você também pode [baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente do SQL Server Data Tools para obter esse utilitário.
- Não há suporte para o arquivamento no armazenamento premium do Azure usando um arquivo BACPAC.
- Se a operação de exportação exceder 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
 - Aumentar temporariamente seu nível de serviço.
 - Interromper toda a atividade de leitura e gravação durante a exportação.
 - Use um [índice clusterizado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir a verificação da tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executar **DBCC SHOW\_STATISTICS** e certificar-se de que o *RANGE\_HI\_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [DBCC SHOW\_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [AZURE.NOTE] BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, consulte [Backups automáticos do Banco de Dados SQL](sql-database-automated-backups.md).

Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure.
- Um banco de dados SQL do Azure.
- Uma [conta de Armazenamento Standard do Azure](../storage/storage-create-storage-account.md) com um contêiner de blob para armazenar o BACPAC no armazenamento standard.


[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]




## Exportar o banco de dados

O cmdlet [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx) envia ao serviço uma solicitação para exportar banco de dados. Dependendo do tamanho do banco de dados, a operação de exportação poderá demorar para ser concluída.

> [AZURE.IMPORTANT] Para garantir um arquivo BACPAC com consistência transacional, primeiro você deve [criar uma cópia do banco de dados](sql-database-copy-powershell.md) e, em seguida, exportar essa cópia.


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## Monitorar o progresso da operação de exportação

Depois de executar **New-AzureRmSqlDatabaseExport**, você poderá verificar o status da solicitação executando [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx). Executar isso imediatamente após a solicitação geralmente retorna **Status : InProgress**. Quando você vir **Status : Succeeded**, a exportação estará concluída.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## Exemplo de exportação de banco de dados SQL

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



## Próximas etapas

- Para saber como importar um Banco de Dados SQL do Azure usando o Powershell, consulte [Importar um BACPAC usando o PowerShell](sql-database-import-powershell.md).


## Recursos adicionais

- [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/mt707796.aspx)
- [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/mt707794.aspx)

<!---HONumber=AcomDC_0817_2016-->