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
	ms.date="04/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Este artigo fornece instruções para arquivar seu Banco de Dados SQL do Azure para um arquivo BACPAC armazenado no armazenamento de blobs do Azure usando o PowerShell.

Quando você precisa criar um arquivo morto de um Banco de Dados SQL do Azure, pode exportar o esquema de banco de dados e os dados para um arquivo BACPAC. Um arquivo BACPAC é simplesmente um arquivo ZIP com uma extensão de BACPAC. Um arquivo BACPAC posteriormente pode ser armazenado no armazenamento de blobs do Azure ou no armazenamento local em um local e posteriormente importado novamente no Banco de Dados SQL do Azure ou em uma instalação local do SQL Server.

***Considerações***

- Para um arquivo morto ser transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou exportação de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu Banco de Dados SQL do Azure.
- O tamanho máximo de um arquivo BACPAC arquivado no armazenamento de blobs do Azure é de 200 GB. Use o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) para arquivar um arquivo BACPAC maior no armazenamento local. Esse utilitário é fornecido com o Visual Studio e o SQL Server. Você também pode [baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente do SQL Server Data Tools para obter esse utilitário.
- Não há suporte para o arquivamento no armazenamento premium do Azure usando um arquivo BACPAC.
- Se a operação de exportação ficar acima de 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
 - Aumentar temporariamente seu nível de serviço 
 - Interromper toda a atividade de leitura e gravação durante a exportação
 - Usar um índice clusterizado em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque os serviços de exportação precisam concluir a verificação de tabela para tentar exportar a tabela inteira
 
> [AZURE.NOTE] BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure. 
- Um banco de dados SQL Azure. 
- Uma [conta de Armazenamento Standard do Azure](../storage/storage-create-storage-account.md) com um contêiner de blob para armazenar o BACPAC no armazenamento standard.
- PowerShell do Azure. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).


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

> [AZURE.IMPORTANT] Para garantir um arquivo BACPAC com consistência transacional, primeiro você deve [criar uma cópia do banco de dados](sql-database-copy-powershell.md) e, em seguida, exportar essa cópia.


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

<!---HONumber=AcomDC_0518_2016-->