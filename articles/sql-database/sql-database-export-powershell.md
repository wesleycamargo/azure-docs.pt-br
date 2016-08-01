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
	ms.date="07/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Arquivar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Este artigo fornece instruções para arquivar seu banco de dados SQL do Azure para um arquivo BACPAC armazenado no armazenamento de blobs do Azure usando o PowerShell.

Quando você precisa criar um arquivo morto de um Banco de Dados SQL do Azure, pode exportar o esquema de banco de dados e os dados para um arquivo BACPAC. Um arquivo BACPAC é simplesmente um arquivo ZIP com uma extensão de BACPAC. Posteriormente, um arquivo BACPAC poderá ser colocado no armazenamento de blobs do Azure ou no armazenamento local em um local e depois importado novamente para o Banco de Dados SQL do Azure ou uma instalação local do SQL Server.

***Considerações***

- Para um arquivo ser transacionalmente consistente, você deve assegurar que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou exportar a partir de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu Banco de Dados SQL do Azure
- O tamanho máximo de um arquivo BACPAC arquivado no armazenamento de blobs do Azure é de 200 GB. Use o utilitário de prompt de comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) para arquivar um arquivo BACPAC maior no armazenamento local. Esse utilitário é fornecido com o Visual Studio e o SQL Server. Você também pode [baixar](https://msdn.microsoft.com/library/mt204009.aspx) a versão mais recente do SQL Server Data Tools para obter esse utilitário.
- Não há suporte para o arquivamento no armazenamento premium do Azure usando um arquivo BACPAC.
- Se a operação de exportação ficar acima de 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:
 - Aumentar temporariamente seu nível de serviço
 - Interromper toda a atividade de leitura e gravação durante a exportação
 - Usar um índice clusterizado em todas as tabelas grandes. Sem índices clusterizados, a exportação poderá falhar se demorar mais de 6 a 12 horas. Isso ocorre porque o serviço de exportação precisa concluir a verificação da tabela para tentar exportar a tabela inteira.
 
> [AZURE.NOTE] BACPACs não devem ser usados para operações de backup e restauração. O Banco de Dados SQL do Azure cria automaticamente backups de todos os bancos de dados de usuário. Para obter detalhes, consulte [Backups automáticos do Banco de Dados SQL](sql-database-automated-backups.md).

Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure.
- Um banco de dados SQL do Azure.
- Uma [conta de Armazenamento Standard do Azure](../storage/storage-create-storage-account.md) com um contêiner de blob para armazenar o BACPAC no armazenamento standard.


[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]


## Configurar as variáveis para o ambiente específico

Existem algumas variáveis em que você precisará substituir os valores de exemplo pelos valores específicos do seu banco de dados e conta de armazenamento.

Substitua por valores específicos.

    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"


No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento para obter esses valores. Você pode encontrar a tecla de acesso primária clicando em **Todas as configurações** e em **Teclas** na folha de sua conta de armazenamento.

    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


A execução do cmdlet **Get-Credential** abrirá uma janela que solicitará seu nome de usuário e senha. Insira o logon de administrador e a senha para o servidor SQL (e NÃO o nome de usuário e a senha da sua conta do Azure).

    $credential = Get-Credential

## Exportar o banco de dados

Este comando envia ao serviço uma solicitação de exportação de banco de dados. Dependendo do tamanho do banco de dados, a operação de exportação poderá demorar a ser concluída.

> [AZURE.IMPORTANT] Para garantir um arquivo BACPAC com consistência transacional, primeiro você deve [criar uma cópia do banco de dados](sql-database-copy-powershell.md) e, em seguida, exportar essa cópia.


    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorPassword $credential.Password
    

## Monitorar o andamento da operação de exportação

Depois de executar **New-AzureRmSqlDatabaseExport**, você poderá verificar o status da solicitação. A execução disso imediatamente depois da solicitação normalmente retornará **Status: Pendente** ou **Status: Em execução**, para que você possa executar isso várias vezes até ver **Status: Concluído** na saída.

A execução desse comando solicitará uma senha. Insira a senha do administrador do SQL Server.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink
    


## Exportar um script do PowerShell do banco de dados SQL


    $ServerName = "servername"
    $StorageName = "storageaccountname"
    $StorageKeyType = "storageKeyType"
    $StorageUri = "http://storageaccountname.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName  $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey  StorageUri $StorageUri –AdministratorLogin $credential.UserName  –AdministratorPassword $credential.Password

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest .OperationStatusLink

## Próximas etapas

- [Importar um banco de dados SQL do Azure](sql-database-import-powershell.md)


## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0720_2016-->