<properties 
    pageTitle="Criar e exportar um BACPAC de um banco de dados SQL do Azure usando o PowerShell" 
    description="Criar e exportar um BACPAC de um banco de dados SQL do Azure usando o PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Criar e exportar um BACPAC de um banco de dados SQL do Azure usando o PowerShell

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Este artigo fornece instruções para exportar um BACPAC do seu banco de dados SQL do Azure com o PowerShell.

Um [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) é um arquivo .bacpac que contém um esquema de banco de dados e dados. O caso de uso primário para um BACPAC é mover um banco de dados de um servidor para outro, [migrar um banco de dados local para a nuvem](sql-database-cloud-migrate.md) e arquivar um banco de dados existente em um formato aberto.

> [AZURE.NOTE]BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).


O BACPAC é exportado para um contêiner de blob de armazenamento do Azure que você poderá baixar depois que a operação for concluída com êxito.


Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL Azure. Se você não tiver um banco de dados SQL, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
- Uma [conta de Armazenamento do Azure](storage-create-storage-account.md) com um contêiner de blob para armazenar o BACPAC. Atualmente, a conta de armazenamento deve usar o modelo de implantação clássica, sendo assim, escolha **Clássico** ao criar uma conta de armazenamento.
- PowerShell do Azure. Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).



## Configurar suas credenciais e selecionar sua assinatura

Em primeiro lugar, você deve estabelecer o acesso à sua conta do Azure e, depois, iniciar o PowerShell e executar o cmdlet a seguir. Na tela de logon, insira o mesmo email e senha que você usa para entrar no portal do Azure.

	Add-AzureAccount

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura, é necessário ter a ID ou o nome da assinatura (**-SubscriptionName**). É possível copiar a ID da assinatura nas informações exibidas na etapa anterior ou, se tiver várias assinaturas e precisar de mais detalhes, você pode executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do resultset. Quando tiver sua assinatura, execute o seguinte cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Depois de executar **Select-AzureSubscription** com êxito, você retornará ao prompt do PowerShell. Se tiver mais de uma assinatura, você poderá executar **Get-AzureSubscription** e verificar se a assinatura que deseja usar mostra **IsCurrent: True**.


## Configurar as variáveis para o ambiente específico

Existem algumas variáveis em que você precisará substituir os valores de exemplo pelos valores específicos do seu banco de dados e conta de armazenamento.

Substitua os nomes de servidor e de banco de dados com o servidor e o banco de dados atualmente em sua conta. Para obter o nome do blob, insira o nome do arquivo BACPAC que será criado. Insira o que quiser como o nome do arquivo BACPAC, mas você deve incluir a extensão .bacpac.

    $ServerName = "servername"
    $DatabaseName = "nameofdatabasetoexport"
    $BlobName = "filename.bacpac"

No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento para obter esses valores. Você pode encontrar a chave de acesso primária clicando em **Todas as configurações** e em **Chaves** na folha da sua conta de armazenamento.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"

## Criar um ponteiro para seu servidor e para a conta de armazenamento

A execução do cmdlet **Get-Credential** abre uma janela que pede seu nome de usuário e senha. Insira o logon de administrador e a senha para o servidor SQL (e NÃO o nome de usuário e a senha da sua conta do Azure).

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Exportar o banco de dados

Este comando envia ao serviço uma solicitação de exportação de banco de dados. Dependendo do tamanho do banco de dados, a operação de exportação poderá demorar a ser concluída.

> [AZURE.IMPORTANT]Para garantir um arquivo BACPAC com consistência transacional, primeiro você deve [criar uma cópia do banco de dados](sql-database-copy-powershell.md) e, em seguida, exportar essa cópia.


    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Monitorar o andamento da operação de exportação

Depois de executar **Start-AzureSqlDatabaseEXport**, você poderá verificar o status da solicitação. A execução disso imediatamente depois da solicitação normalmente retornará **Status: Pendente** ou **Status: Em execução**, para que você possa executar isso várias vezes até ver **Status: Concluído** na saída.

A execução desse comando solicitará uma senha. Insira a senha do administrador do SQL Server.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Exportar um script do PowerShell do banco de dados SQL


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "databasename"
    $BlobName = "bacpacfilename"
    
    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Próximas etapas

- [Importar um banco de dados SQL do Azure](sql-database-import-powershell.md)


## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->