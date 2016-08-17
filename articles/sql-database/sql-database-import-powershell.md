<properties
    pageTitle="Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure usando o PowerShell | Microsoft Azure"
    description="Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure usando o PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="07/06/2016"
    ms.author="sstein"/>

# Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure usando o PowerShell

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Este artigo fornece instruções para criar um banco de dados SQL do Azure importando um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) com o PowerShell.

O banco de dados é criado de um arquivo BACPAC importado de um contêiner do Azure Storage Blob. Se você não tiver um arquivo .bacpac no armazenamento do Azure, poderá criar um seguindo as etapas em [Arquivar um Banco de Dados SQL do Azure em um arquivo BACPAC usando o PowerShell](sql-database-export-powershell.md).

> [AZURE.NOTE] O Banco de Dados SQL do Azure cria e mantém backups automaticamente de cada banco de dados de usuário que você pode restaurar. Para obter detalhes, consulte [Backups automáticos do Banco de Dados SQL](sql-database-automated-backups.md).


Para importar um banco de dados SQL, você precisará de:

- Uma assinatura do Azure. Caso você precise de uma assinatura do Azure, basta clicar em **Avaliação Gratuita** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um arquivo .bacpac do banco de dados que você deseja importar. O BACPAC deve estar em um contêiner de blob da [conta de armazenamento do Azure (clássico)](../storage/storage-create-storage-account.md).



[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]



## Configurar as variáveis do ambiente

Existem algumas variáveis em que você precisará substituir os valores de exemplo pelos valores específicos do seu banco de dados e conta de armazenamento.

O nome do servidor deve ser um servidor que existe atualmente na assinatura selecionada na etapa anterior. Ele deve ser o servidor no qual você deseja que o banco de dados seja criado. Observe que não há suporte para importação de um banco de dados diretamente para um pool elástico. No entanto, você pode primeiro importar para um banco de dados individual e, em seguida, mover o banco de dados para um pool.

O nome do banco de dados é o nome desejado para o novo banco de dados.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


As variáveis a seguir são da conta de armazenamento onde seu BACPAC está localizado. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento para obter esses valores. Você pode encontrar a tecla de acesso primária clicando em **Todas as configurações** e em **Teclas** na folha de sua conta de armazenamento.

O nome do blob é o nome de um arquivo .bacpac existente do qual você deseja criar o banco de dados. Você precisa incluir a extensão .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


A execução do cmdlet **Get-Credential** abrirá uma janela que solicitará seu nome de usuário e senha. Insira o logon e a senha de administrador do servidor do Banco de Dados SQL ($ServerName acima) e não o nome de usuário e a senha da sua conta do Azure.

    $credential = Get-Credential


## Importar o banco de dados

Esse comando envia ao serviço uma solicitação para importar o banco de dados. Dependendo do tamanho do banco de dados, a operação de importação pode demorar para ser concluída.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## Monitorar o andamento da operação

Depois de executar **New-AzureRmSqlDatabaseImport**, você poderá verificar o status da solicitação.

Executar isso imediatamente após a solicitação geralmente retorna **Status : Pending** ou **Status : Running**. Execute esse procedimento várias vezes até ver **Status : Completed** na saída.

A execução desse comando solicitará uma senha. Insira a senha do administrador do SQL Server.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink



## Script de importação do PowerShell do banco de dados SQL


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest .OperationStatusLink



## Próximas etapas

- Para aprender a se conectar e a consultar um Banco de Dados SQL importado, consulte [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)

<!---HONumber=AcomDC_0803_2016-->