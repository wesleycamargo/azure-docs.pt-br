<properties
   pageTitle="Recuperar um banco de dados de uma interrupção no SQL Data Warehouse | Microsoft Azure"
   description="Etapas para recuperar um banco de dados de uma interrupção no SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="sahajs;barbkess"/>

# Recuperar um banco de dados de uma interrupção no SQL Data Warehouse

A restauração geográfica possibilita restaurar um banco de dados por meio de um backup com redundância geográfica para criar um novo banco de dados. O banco de dados pode ser criado em qualquer servidor em qualquer região do Azure. Como usa um backup com redundância geográfica como sua fonte, ele pode ser usado para recuperar um banco de dados mesmo que este esteja inacessível devido a uma interrupção. Além de recuperar de uma interrupção, a restauração geográfica também pode ser usada em outros cenários, como migrar ou copiar o banco de dados para um servidor ou região diferente.


## Quando iniciar a recuperação
A operação de recuperação exige a alteração da cadeia de conexão SQL no momento da recuperação e pode resultar em perda permanente de dados. Portanto, deve ser feita somente quando houver a probabilidade de a interrupção durar mais do que o RTO do aplicativo. Use os seguintes pontos de dados para declarar que a recuperação é garantida:

- Falha de conectividade permanente no banco de dados.
- Seu Portal do Azure mostra um alerta sobre um incidente na região com grande impacto.


## Recuperação usando a restauração geográfica
Recuperar um banco de dados cria um novo banco de dados do último backup com redundância geográfica. É importante garantir que o servidor que você está recuperando tenha capacidade de DTU suficiente para o novo banco de dados. Você pode solicitar um aumento dessa cota [contatando o suporte][].


### Portal do Azure
1. Faça logon no [Portal do Azure][].
2. No lado esquerdo da tela, selecione **+NOVO**, selecione **Dados e Armazenamento** e selecione **SQL Data Warehouse**
3. Selecione **BACKUP** como a origem e selecione o backup redundante geograficamente do qual você deseja recuperar
4. Especifique o restante das propriedades do banco de dados e clique em **Criar**
5. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**


### PowerShell
Use o Azure PowerShell para executar a recuperação de banco de dados programaticamente. Para baixar o módulo PowerShell do Azure, execute o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Você pode verificar a versão executando Get-Module -ListAvailable -Name Azure. Este artigo baseia-se na versão 1.0.4 do Microsoft Azure PowerShell.

Para recuperar um banco de dados, use o cmdlet [Start-AzureSqlDatabaseRecovery][].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Obtenha o banco de dados que você deseja recuperar.
5. Crie a solicitação de recuperação para o banco de dados.
6. Monitore o progresso da recuperação.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$Database = Get-AzureRmSqlRecoverableDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Recover database
$RecoveryRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourSourceServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -TargetServerName "<YourTargetServerName>"

# Monitor progress of recovery operation
Get-AzureSqlDatabaseOperation -ServerName "<YourTargetServerName>" –OperationGuid $RecoveryRequest.RequestID

```

Observe que, se o servidor for foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do Powershell acima.

### API REST
Use REST para executar a recuperação do banco de dados de forma programática.

1. Obtenha a lista de bancos de dados recuperáveis usando a operação [Listar bancos de dados recuperáveis][].
2. Obtenha o banco de dados que você deseja recuperar usando a operação [Obter banco de dados recuperável][].
3. Crie a solicitação de recuperação usando a operação [Criar solicitação de recuperação de banco de dados][].
4. Acompanhe o status da recuperação usando a operação [Status da operação de banco de dados.][].



## Configurar o banco de dados após a recuperação
Esta é uma lista de verificação para ajudar a preparar para produção seu banco de dados recuperado.

1. **Atualizar cadeias de conexão**: verifique se as cadeias de conexão das ferramentas cliente estão apontando para o banco de dados recentemente recuperado.
2. **Modificar as regras de firewall**: verifique as regras de firewall no servidor de destino para ter certeza de que as conexões dos computadores cliente ou do Azure com o servidor e com o banco de dados recentemente recuperado estão habilitadas.
3. **Verificar logons do servidor e usuários do banco de dados**: verifique se todos os logons usados pelo aplicativo existem no servidor que está hospedando o banco de dados recuperado. Recrie os logons ausentes e conceda a eles permissões apropriadas no banco de dados recuperado. 
4. **Habilitar a auditoria**: se a auditoria for necessária para acessar o banco de dados, você precisará habilitar a auditoria após a recuperação do banco de dados.

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.


## Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios de outras edições do Banco de Dados SQL do Azure, leia a [Visão geral de continuidade de negócios do Banco de Dados SQL do Azure][].


<!--Image references-->

<!--Article references-->
[Visão geral de continuidade de negócios do Banco de Dados SQL do Azure]: sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Start-AzureSqlDatabaseRecovery]: https://msdn.microsoft.com/library/azure/dn720224.aspx
[Listar bancos de dados recuperáveis]: http://msdn.microsoft.com/library/azure/dn800984.aspx
[Obter banco de dados recuperável]: http://msdn.microsoft.com/library/azure/dn800985.aspx
[Criar solicitação de recuperação de banco de dados]: http://msdn.microsoft.com/library/azure/dn800986.aspx
[Status da operação de banco de dados.]: http://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Other Web references-->
[Portal do Azure]: https://portal.azure.com/
[contatando o suporte]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0330_2016-->