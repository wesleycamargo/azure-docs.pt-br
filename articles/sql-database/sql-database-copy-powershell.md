<properties 
    pageTitle="Copiar um banco de dados SQL do Azure usando o PowerShell | Microsoft Azure" 
    description="Criar cópia de um Banco de Dados SQL do Azure usando o PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Copiar um banco de dados SQL do Azure usando o PowerShell


> [AZURE.SELECTOR]
- [Visão geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

As etapas a seguir mostram como copiar um banco de dados SQL com PowerShell para o mesmo servidor ou outro servidor. A operação de cópia do banco de dados usa o cmdlet [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx).


Para concluir este artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL Azure. Se você não tiver um banco de dados SQL, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
- PowerShell do Azure. Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).



## Copiar seu banco de dados SQL

Existem algumas variáveis em que você precisará substituir os valores de exemplo pelos valores específicos do seu banco de dados e dos servidores. Substitua os valores de espaço reservado pelos valores para o seu ambiente:

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





### Copiar um banco de dados SQL para o mesmo servidor

Esse comando envia a solicitação de cópia do banco de dados para o serviço. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

### Copiar um banco de dados SQL para um servidor diferente

Esse comando envia a solicitação de cópia do banco de dados para o serviço. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## Monitorar o andamento da operação de cópia

Depois de executar **Start-AzureSqlDatabaseCopy**, você poderá verificar o status da solicitação de cópia. A execução disso imediatamente após a solicitação normalmente retornará **Estado: Pendente** ou **Estado: Em execução**, para que você possa executar isso várias vezes até ver **Estado: CONCLUÍDO** na saída.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName

## Resolver logons

Para resolver logons após a conclusão da operação de cópia, confira [Resolver logons](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## Exemplo de script do PowerShell

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

- Confira [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md) para ter uma visão geral de como copiar um Banco de Dados SQL do Azure.
- Confira [Copy an Azure SQL database using the Azure Portal (Copiar um banco de dados SQL do Azure usando o Portal do Azure)](sql-database-copy-portal.md) para copiar um banco de dados usando o Portal do Azure.
- Confira [Copiar um banco de dados SQL do Azure usando o Transact-SQL](sql-database-copy-transact-sql.md) para copiar um banco de dados usando o Transact-SQL.
- Confira [Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md) para saber mais sobre como gerenciar logons e usuários ao copiar um banco de dados para um servidor lógico diferente.


## Recursos adicionais

- [Gerenciar logons](sql-database-manage-logins.md)
- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)
- [Exportar o banco de dados para um BACPAC](sql-database-export.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0622_2016-->