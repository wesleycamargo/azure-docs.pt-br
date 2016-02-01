<properties 
    pageTitle="Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure usando o PowerShell | Microsoft Azure" 
    description="Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure usando o PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="01/20/2016"
    ms.author="sstein"/>

# Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure usando o PowerShell

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Este artigo fornece instruções para criar um banco de dados SQL do Azure importando um BACPAC com o PowerShell.

Um BACPAC é um arquivo .bacpac que contém um esquema de banco de dados e dados. Para obter detalhes, consulte Pacote de backup (.bacpac) em [Aplicativos de camada de dados](https://msdn.microsoft.com/library/ee210546.aspx).

O banco de dados é criado de um BACPAC importado de um contêiner de blob de armazenamento do Azure. Se não tiver um arquivo .bacpac no armazenamento do Azure, você poderá criar um seguindo as etapas descritas em [Criar e exportar um BACPAC de um Banco de Dados SQL do Azure](sql-database-export-powershell.md).

> [AZURE.NOTE]O Banco de Dados SQL do Azure cria e mantém backups automaticamente de cada banco de dados de usuário que você pode restaurar. Para obter detalhes, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).


Para importar um banco de dados SQL, você precisará de:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um arquivo .bacpac (BACPAC) do banco de dados que deseja restaurar. O BACPAC deve estar em um contêiner de blob da [conta de armazenamento do Azure (clássico)](storage-create-storage-account.md).


> [AZURE.IMPORTANT]Este artigo contém comandos para versões do Azure PowerShell, *exceto* as versões 1.0 e posteriores. É possível verificar sua versão do Azure PowerShell com o comando **Get-Module azure | format-table version**.



## Configurar suas credenciais e selecionar sua assinatura

Em primeiro lugar, você deve estabelecer o acesso à sua conta do Azure e, depois, iniciar o PowerShell e executar o cmdlet a seguir. Na tela de logon, insira o mesmo email e senha que você usa para entrar no portal do Azure.

	Add-AzureAccount

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura, você precisa da ID da assinatura. É possível copiar a ID de assinatura das informações exibidas na etapa anterior ou, se tiver várias assinaturas e precisar de mais detalhes, você poderá executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do consulto de resultados. De posse da Id da assinatura, execute o seguinte cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Depois de executar **Select-AzureSubscription** com êxito, você retornará ao prompt do PowerShell. Se tiver mais de uma assinatura, você poderá executar **Get-AzureSubscription** e verificar se a assinatura selecionada mostra **IsCurrent: True**.


## Configurar as variáveis de ambiente

Existem algumas variáveis em que você precisará substituir os valores de exemplo pelos valores específicos do seu banco de dados e conta de armazenamento.

O nome do servidor precisa ser de um servidor que exista atualmente na assinatura selecionada na etapa anterior e seja o servidor em que deseja que o banco de dados seja criado.

O nome do banco de dados é o nome desejado para o novo banco de dados.

    $ServerName = "servername"
    $DatabaseName = "databasename"


As variáveis a seguir são da conta de armazenamento onde seu BACPAC está localizado. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento para obter esses valores. Você pode encontrar a tecla de acesso primária clicando em **Todas as configurações** e em **Teclas** na folha de sua conta de armazenamento.

O nome do blob é o nome de um arquivo .bacpac existente do qual você deseja criar o banco de dados. Você precisa incluir a extensão .bacpac.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## Criar um ponteiro para seu servidor e conta de armazenamento

A execução do cmdlet **Get-Credential** abrirá uma janela que solicitará seu nome de usuário e senha. Insira o logon e a senha de administrador do SQL Server no qual deseja criar o banco de dados ($ServerName acima) e não o nome de usuário e a senha da sua conta do Azure.

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Importar o banco de dados

Esse comando envia ao serviço uma solicitação para importar o banco de dados. Dependendo do tamanho do banco de dados, a operação de importação pode demorar a ser concluída.

    $importRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Monitorar o andamento da operação

Depois de executar **Start-AzureSqlDatabaseImport**, você poderá verificar o status da solicitação.

Verificar o status logo após a solicitação geralmente retorna o status **Pendente** ou **Em execução** e fornecerá a porcentagem atual concluída, para que você possa fazer isso várias vezes até ver **Status: Concluído** na saída.

A execução desse comando solicitará uma senha. Insira o logon e a senha de administrador do SQL Server.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## Script de restauração do PowerShell do Banco de Dados SQL


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseImport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## Próximas etapas

- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)




## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0121_2016-->