<properties 
   pageTitle="Recuperação de desastres do Banco de Dados SQL" 
   description="Saiba como recuperar um banco de dados de uma interrupção do datacenter regional ou de uma falha com os recursos de Replicação geográfica ativa, Replicação geográfica padrão e Restauração geográfica do Banco de Dados SQL do Azure." 
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
   ms.date="11/09/2015"
   ms.author="elfish"/>

# Recuperar um Banco de Dados SQL do Azure de uma interrupção

O Banco de Dados SQL do Azure oferece os seguintes recursos para a recuperação de uma paralisação:

- Replicação geográfica ativa [(blog)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- Replicação geográfica padrão [(blog)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- Restauração geográfica [(blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)
- Novos recursos de replicação geográfica [(blog)](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)

Para saber mais sobre a preparação para desastres e quando recuperar o banco de dados, visite nossa página [Design para continuidade dos negócios](sql-database-business-continuity-design.md).

##Quando iniciar a recuperação 

A operação de recuperação afeta o aplicativo. Ele exige a alteração da cadeia de conexão SQL e pode resultar em perda permanente de dados. Portanto, deve ser feita somente quando houver a probabilidade de a interrupção durar mais do que o RTO do aplicativo. Quando o aplicativo for implantado na produção você deverá executar o monitoramento regular da integridade do aplicativo e usar os seguintes pontos de dados para ter certeza de a recuperação é garantida:

1. Falha de conectividade permanente da camada do aplicativo com o banco de dados.
2. Seu Portal Clássico do Azure mostra um alerta sobre um incidente na região com grande impacto.

> [AZURE.NOTE]Depois que o banco de dados é recuperado, você pode configurá-lo para ser usado, seguindo o guia [Configurar seu banco de dados após a recuperação](#postrecovery).

## Failover para banco de dados secundário replicado geograficamente
> [AZURE.NOTE]Deve ser configurado para ter um banco de dados secundário a ser usado para failover. A replicação geográfica só está disponível para bancos de dados Standard e Premium. Saiba [como configurar a replicação geográfica](sql-database-business-continuity-design.md)

###Portal Clássico do Azure
Use o Portal Clássico do Azure para encerrar o relacionamento de cópia contínuo com o banco de dados secundário replicado geograficamente.

1. Faça logon no [Portal Clássico do Azure](https://portal.Azure.com)
2. No lado esquerdo da tela, selecione **PROCURAR** e selecione **Bancos de Dados SQL**
3. Navegue até o banco de dados e selecione-o. 
4. Na parte inferior da folha do banco de dados, selecione o **Mapa de Replicação Geográfica**.
4. Em **Secundários** clique com o botão direito do mouse na linha com o nome do banco de dados que você deseja recuperar e selecione **Failover**.

###PowerShell
Use o PowerShell para iniciar o failover para o banco de dados secundário replicado geograficamente usando o cmdlet [Set-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).
		
		$database = Get-AzureRMSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
		$database | Set-AzureRMSqlDatabaseSecondary –Failover -AllowDataLoss

###API REST 
Use REST para iniciar programaticamente o failover para um banco de dados secundário.

1. Obtenha o link de replicação para um secundário específico usando a operação [Obter link de replicação](https://msdn.microsoft.com/library/mt600778.aspx).
2. Faça failover para o secundário usando [Definir banco de dados secundário como principal](https://msdn.microsoft.com/library/mt582027.aspx) com perda de dados permitida. 

## Recuperação usando a restauração geográfica

Caso ocorra uma interrupção de um banco de dados, você poderá recuperar o banco de dados a partir de seu backup redundante geograficamente mais recente usando a Restauração Geográfica.

> [AZURE.NOTE]A recuperação de um banco de dados cria um novo banco de dados. É importante garantir que o servidor que você está recuperando tenha capacidade de DTU suficiente para o novo banco de dados. Você pode solicitar um aumento dessa cota [contatando o suporte](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portal Clássico do Azure
Para restaurar um banco de dados SQL usando a restauração geográfica no Portal Clássico do Azure, use as etapas a seguir.

1. Faça logon no [Portal Clássico do Azure](https://portal.Azure.com)
2. No lado esquerdo da tela, selecione **NOVO** e selecione **Dados e Armazenamento** e selecione **Banco de Dados SQL**
2. Selecione **BACKUP** como a origem e, em seguida, selecione o backup redundante geograficamente do qual você deseja recuperar.
3. Especifique o restante das propriedades do banco de dados e, em seguida, clique em **Criar**.
4. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**, no lado esquerdo da tela.

###PowerShell 
Para restaurar um Banco de Dados SQL usando a Restauração geográfica com o PowerShell, inicie uma solicitação de restauração geográfica com o cmdlet [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

###API REST 

Use REST para executar a recuperação do banco de dados de forma programática.

1.	Obtenha a lista de bancos de dados recuperáveis usando a operação [Listar bancos de dados recuperáveis](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Obtenha o banco de dados que você deseja recuperar usando a operação [Obter banco de dados recuperável](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Crie a solicitação de recuperação usando a operação [Criar solicitação de recuperação de banco de dados](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Acompanhe o status da recuperação usando a operação [Status da operação de banco de dados.](http://msdn.microsoft.com/library/azure/dn720371.aspx).
 
## Configurar o banco de dados após a recuperação<a name="postrecovery"></a>

Esta é uma lista de verificação de tarefas que pode ser usada para ajudar a preparar para produção seu banco de dados recuperado.

### Atualizar cadeias de conexão

Verifique se as cadeias de conexão do aplicativo estão apontando para o banco de dados recentemente recuperado. Atualize as cadeias de conexão no caso de uma das situações abaixo:

  + O banco de dados recuperado usa um nome diferente do nome do banco de dados de origem
  + O banco de dados recuperado está em um servidor diferente do servidor de origem

Para obter mais informações sobre como alterar as cadeias de conexão, consulte [Conexões com o Banco de Dados SQL do Azure: recomendações centrais](sql-database-connect-central-recommendations.md).
 
### Modificar regras de firewall
Verifique as regras de firewall no nível de servidor e de banco de dados para ter certeza de que as conexões dos computadores cliente ou do Azure com o servidor e com o banco de dados recentemente recuperado estão habilitadas. Para saber mais, consulte [Como definir configurações de firewall (Banco de Dados SQL do Azure)](sql-database-configure-firewall-settings.md).

### Verificar logons do servidor e usuários do banco de dados

Verifique se todos os logons usados pelo aplicativo existem no servidor que está hospedando o banco de dados recuperado. Recrie os logons ausentes e conceda a eles permissões apropriadas no banco de dados recuperado. Para saber mais, confira [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

Verifique se cada usuário no banco de dados recuperado está associado a um logon de servidor válido. Use a instrução ALTER USER para mapear o usuário para o logon de servidor válido. Para saber mais, confira [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486).


### Configurar alertas de telemetria

Verifique se as configurações de regra de alerta existentes foram mapeadas para o banco de dados recuperado. Atualize a configuração no caso de uma das situações abaixo:

  + O banco de dados recuperado usa um nome diferente do nome do banco de dados de origem
  + O banco de dados recuperado está em um servidor diferente do servidor de origem

Para obter mais informações sobre regras de alerta de banco de dados, consulte [Receber notificações de alerta](insights-receive-alert-notifications.md) e [Acompanhar a integridade do serviço](insights-service-health.md).


### Habilitar a auditoria

Se a auditoria for necessária para acessar o banco de dados, você precisará habilitar a auditoria após a recuperação do banco de dados. Um bom indicador de auditoria é obrigatório, pois os aplicativos cliente usam cadeias de conexão seguras em um padrão *.database.secure.windows.net. Para saber mais, confira [Introdução à Auditoria do Banco de Dados SQL](sql-database-auditing-get-started.md).

<!---HONumber=AcomDC_1210_2015-->