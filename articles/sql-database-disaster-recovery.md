<properties 
   pageTitle="Recuperação de desastres do Banco de Dados SQL" 
   description="Saiba como recuperar um banco de dados de uma interrupção do datacenter regional ou de uma falha com os recursos de Replicação geográfica e Restauração geográfica do Banco de Dados SQL do Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="elfish"/>

# Recuperar um Banco de Dados SQL do Azure de uma interrupção

O Banco de Dados SQL do Azure oferece alguns recursos de recuperação de interrupção:

- Replicação geográfica ativa [(blog)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- Replicação geográfica padrão [(blog)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- Restauração geográfica [(blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

Para saber mais sobre a preparação para desastres e quando recuperar o banco de dados, visite nossa página [Design para continuidade dos negócios](sql-database-business-continuity-design.md).

##Quando iniciar a recuperação 

A operação de recuperação afeta o aplicativo. Ele exige a alteração da cadeia de conexão SQL e pode resultar em perda permanente de dados. Portanto, deve ser feita somente quando houver a probabilidade de a interrupção durar mais do que o RTO do aplicativo. Quando o aplicativo for implantado na produção você deverá executar o monitoramento regular da integridade do aplicativo e usar os seguintes pontos de dados para ter certeza de a recuperação é garantida:

1. Falha de conectividade permanente da camada do aplicativo com o banco de dados.
2. Seu Portal do Azure mostra um alerta sobre um incidente na região com grande impacto.

## Failover para o banco de dados secundário replicado geograficamente
> [AZURE.NOTE]Você deve configurar a [Replicação geográfica padrão](https://msdn.microsoft.com/library/azure/dn758204.aspx) ou a [Replicação geográfica](https://msdn.microsoft.com/library/azure/dn741339.aspx) para ter um banco de dados secundário a ser usado para o failover. A replicação geográfica só está disponível para bancos de dados Standard e Premium.

Caso ocorra uma interrupção no banco de dados primário, será possível fazer failover para um banco de dados secundário a fim de restaurar a disponibilidade. Para fazer isso, você precisará forçar o encerramento da relação de cópia contínua. Para obter uma descrição completa do encerramento da relação de cópia contínua, clique [aqui](https://msdn.microsoft.com/library/azure/dn741323.aspx).



###Portal do Azure
1. Faça logon no [Portal do Azure](https://portal.Azure.com)
2. No lado esquerdo da tela, selecione **PROCURAR** e selecione **Bancos de Dados SQL**
3. Navegue até o banco de dados e selecione-o. 
4. Na parte inferior da folha do banco de dados, selecione o **Mapa de Replicação Geográfica**.
4. Em **Secundários** clique com o botão direito do mouse na linha com o nome do banco de dados que você deseja recuperar e selecione **Parar**.

Após o término da relação de cópia contínua, você pode configurar o banco de dados recuperado para ser usado de acordo com o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).
###PowerShell
Use o PowerShell para executar a recuperação do banco de dados de forma programática.

Para encerrar a relação do banco de dados secundário, use o cmdlet [Stop-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720223).
		
		$myDbCopy = Get-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName"
		$myDbCopy | Stop-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -ForcedTermination
		 
Após o término da relação de cópia contínua, você pode configurar o banco de dados recuperado para ser usado de acordo com o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).
###API REST 
Use REST para executar a recuperação do banco de dados de forma programática.

1. Obtenha a cópia contínua do banco de dados usando a operação [Obter cópia de banco de dados](https://msdn.microsoft.com/library/azure/dn509570.aspx).
2. Interrompa a cópia contínua do banco de dados usando a operação [Parar cópia de banco de dados](https://msdn.microsoft.com/library/azure/dn509573.aspx). Use o nome do servidor secundário e o nome do banco de dados no URI da solicitação Stop Database Copy

 Após o término da relação de cópia contínua, você pode configurar o banco de dados recuperado para ser usado de acordo com o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).
## Recuperação usando restauração geográfica

Caso ocorra uma interrupção de um banco de dados, você poderá recuperar o banco de dados a partir de seu backup redundante geograficamente mais recente usando a Restauração Geográfica.

###Portal do Azure
1. Faça logon no [Portal do Azure](https://portal.Azure.com)
2. No lado esquerdo da tela, selecione **NOVO** e selecione **Dados e Armazenamento** e selecione **Banco de Dados SQL**
2. Selecione **BACKUP** como a origem e, em seguida, selecione o backup redundante geograficamente do qual você deseja recuperar.
3. Especifique o restante das propriedades do banco de dados e, em seguida, clique em **Criar**.
4. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**, no lado esquerdo da tela.

Após a recuperação do banco de dados, você poderá configurá-lo para uso seguindo o guia [Finalizar um banco de dados recuperado](articles/sql-database-recovered-finalize.md).
###PowerShell 
Use o PowerShell para executar a recuperação do banco de dados de forma programática.

Para iniciar uma solicitação de Restauração geográfica, use o cmdlet [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx). Para ver um passo a passo detalhado, confira nosso [vídeo explicativo](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell/).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

Após a recuperação do banco de dados, você poderá configurá-lo para uso seguindo o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).
###API REST 

Use REST para executar a recuperação do banco de dados de forma programática.

1.	Obtenha a lista de bancos de dados recuperáveis usando a operação [Listar bancos de dados recuperáveis](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Obtenha o banco de dados que você deseja recuperar usando a operação [Obter banco de dados recuperável](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Crie a solicitação de recuperação usando a operação [Criar solicitação de recuperação de banco de dados](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Acompanhe o status da recuperação usando a operação [Status da operação de banco de dados.](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Após a recuperação do banco de dados, você poderá configurá-lo para uso seguindo o guia [Finalizar um banco de dados recuperado](sql-database-recovered-finalize.md).

<!---HONumber=58-->