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
   ms.date="04/20/2016"
   ms.author="sahajs;barbkess"/>

# Recuperar um banco de dados de uma interrupção no SQL Data Warehouse

A restauração geográfica fornece a capacidade de restaurar um banco de dados de um backup com redundância geográfica. O banco de dados restaurado pode ser criado em qualquer servidor em qualquer região do Azure. Como a restauração geográfica usa um backup com redundância geográfica como sua fonte, ele pode ser usado para recuperar um banco de dados mesmo que este esteja inacessível devido a uma interrupção. Além de recuperar de uma interrupção, a restauração geográfica também pode ser usada em outros cenários, como na migração ou na cópia do banco de dados para um servidor ou região diferente.

## Quando iniciar a recuperação
A operação de recuperação exige a alteração da cadeia de conexão SQL no momento da recuperação e pode resultar em perda permanente de dados. Portanto, deve ser feita somente quando houver a probabilidade de a interrupção durar mais do que o RTO do aplicativo. Use os seguintes pontos de dados para declarar que a recuperação é garantida:

- Falha de conectividade permanente no banco de dados.
- Seu Portal do Azure mostra um alerta sobre um incidente na região com grande impacto.

## Recuperação usando a restauração geográfica
Recuperar um banco de dados cria um novo banco de dados do último backup com redundância geográfica. É importante garantir que o servidor que você está restaurando tenha capacidade de DTU suficiente para o novo banco de dados. Consulte esta postagem no blog para obter mais informações sobre [como exibir e aumentar a cota de DTU][].

### Portal do Azure
1. Faça logon no [portal do Azure][]
2. No lado esquerdo da tela, selecione **+NOVO**, selecione **Dados e Armazenamento** e selecione **SQL Data Warehouse**
3. Selecione **BACKUP** como a origem e selecione o backup redundante geograficamente do qual você deseja recuperar
4. Especifique o restante das propriedades do banco de dados e clique em **Criar**
5. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**

### PowerShell

Para recuperar um banco de dados, use o cmdlet [Restore-AzureRmSqlDatabase][].

> [AZURE.NOTE]  Para usar o Azure Powershell com o SQL Data Warehouse, você precisará instalar a versão 1.0.3 ou superior do Azure PowerShell. Você pode verificar a versão executando **Get-Module -ListAvailable -Name Azure**. A versão mais recente pode ser instalada pelo [Microsoft Web Platform Installer][]. Para obter mais informações sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][].

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Obtenha o banco de dados que você deseja recuperar.
5. Crie a solicitação de recuperação para o banco de dados.
6. Verifique o status do banco de dados com restauração geográfica.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

>[AZURE.NOTE] Para o servidor foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do Powershell acima.

### API REST
Use REST para executar a recuperação do banco de dados de forma programática.

1. Obtenha a lista de bancos de dados recuperáveis usando a operação [Listar bancos de dados recuperáveis][].
2. Obtenha o banco de dados que você deseja recuperar usando a operação [Obter banco de dados recuperável][].
3. Crie a solicitação de recuperação usando a operação [Criar solicitação de recuperação de banco de dados][].
4. Acompanhe o status da recuperação usando a operação [Status da operação de banco de dados.][].

## Configurar o banco de dados após a recuperação
Esta é uma lista de verificação para ajudar a preparar para produção seu banco de dados recuperado.

1. **Atualizar Cadeias de Conexão**: verifique se as cadeias de conexão das ferramentas cliente estão apontando para o banco de dados recentemente recuperado.
2. **Modificar as Regras de Firewall**: verifique as regras de firewall no servidor de destino para ter certeza de que as conexões dos computadores cliente ou do Azure com o servidor e com o banco de dados recentemente recuperado estão habilitadas.
3. **Verificar Logons do Servidor e Usuários do Banco de Dados**: verifique se todos os logons usados pelo aplicativo existem no servidor que está hospedando o banco de dados recuperado. Recrie os logons ausentes e conceda a eles permissões apropriadas no banco de dados recuperado. 
4. **Habilitar a Auditoria**: se a auditoria for necessária para acessar o banco de dados, você precisará habilitar a Auditoria após a recuperação do banco de dados.

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.

## Próximas etapas
Para saber mais sobre os recursos de continuidade de negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral de continuidade de negócios do Banco de Dados SQL do Azure][].

<!--Image references-->

<!--Article references-->
[Como instalar e configurar o Azure PowerShell]: ../powershell/powershell-install-configure.md
[Visão geral de continuidade de negócios do Banco de Dados SQL do Azure]: ../sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Listar bancos de dados recuperáveis]: https://msdn.microsoft.com/library/azure/dn800984.aspx
[Obter banco de dados recuperável]: https://msdn.microsoft.com/library/azure/dn800985.aspx
[Criar solicitação de recuperação de banco de dados]: https://msdn.microsoft.com/library/azure/dn800986.aspx
[Status da operação de banco de dados.]: https://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Blog references-->
[como exibir e aumentar a cota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[portal do Azure]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0518_2016-->