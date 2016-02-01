<properties 
    pageTitle="Criar uma cópia de um banco de dados SQL do Azure usando o PowerShell | Microsoft Azure" 
    description="Criar cópia de um Banco de Dados SQL do Azure usando o PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="01/20/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Criar uma cópia de um Banco de Dados SQL do Azure usando o PowerShell

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



As etapas a seguir mostram como copiar um banco de dados SQL com o PowerShell. A operação de cópia do banco de dados copia um banco de dados SQL para um novo banco de dados usando o cmdlet [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx). A cópia é um backup de instantâneo do banco de dados que você cria no mesmo servidor ou em um servidor diferente.

> [AZURE.NOTE]O Banco de Dados SQL do Azure cria e mantém backups automaticamente de cada banco de dados de usuário que você pode restaurar. Para obter detalhes, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

Quando o processo de cópia for concluído, o novo banco de dados será um banco de dados totalmente funcional independente do banco de dados de origem. O novo banco de dados é transacionalmente consistente com o banco de dados de origem no momento da conclusão da cópia. O desempenho e a camada de nível de serviço (faixa de preço) da cópia do banco de dados são iguais aos do banco de dados de origem. Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Os logons, os usuários e as permissões podem ser gerenciados independentemente.


Quando você copia um banco de dados no mesmo servidor lógico, os mesmos logons podem ser usados em ambos os bancos de dados. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados (DBO) do banco de dados. Todos os usuários do banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados.


Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL Azure. Se você não tiver um banco de dados SQL, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
- PowerShell do Azure. Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).



## Configurar suas credenciais e selecionar sua assinatura

Em primeiro lugar, você deve estabelecer o acesso à sua conta do Azure e, depois, iniciar o PowerShell e executar o cmdlet a seguir. Na tela de logon, insira o mesmo email e senha que você usa para entrar no portal clássico do Azure.

	Add-AzureAccount

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura, é necessário ter a ID ou o nome da assinatura (**-SubscriptionName**). É possível copiar a ID da assinatura nas informações exibidas na etapa anterior ou, se tiver várias assinaturas e precisar de mais detalhes, você pode executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do resultset. Quando tiver sua assinatura, execute o seguinte cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Depois de executar **Select-AzureSubscription** com êxito, você retornará ao prompt do PowerShell. Se tiver mais de uma assinatura, você poderá executar **Get-AzureSubscription** e verificar se a assinatura que deseja usar mostra **IsCurrent: True**.


## Configurar as variáveis para o ambiente específico

Existem algumas variáveis em que você precisará substituir os valores de exemplo pelos valores específicos do seu banco de dados e dos servidores.

Substitua os valores de espaço reservado pelos valores para o seu ambiente:

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





## Copiar um banco de dados SQL para o mesmo servidor

Esse comando envia a solicitação de cópia do banco de dados para o serviço. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

## Copiar um banco de dados SQL para um servidor diferente

Esse comando envia a solicitação de cópia do banco de dados para o serviço. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## Monitorar o andamento da operação de cópia

Depois de executar **Start-AzureSqlDatabaseCopy**, você poderá verificar o status da solicitação de cópia. A execução disso imediatamente após a solicitação normalmente retornará **Estado: Pendente** ou **Estado: Em execução**, para que você possa executar isso várias vezes até ver **Estado: CONCLUÍDO** na saída.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


## Copiar um script do PowerShell do banco de dados SQL

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## Próximas etapas

- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)
- [Exportar o banco de dados para um BACPAC](sql-database-export-powershell.md)


## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0121_2016-->