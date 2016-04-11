<properties
	pageTitle="Atualizar para o Banco de Dados SQL V12 usando o PowerShell | Microsoft Azure"
	description="Explica como atualizar para o Banco de Dados SQL V12 do Azure, incluindo como atualizar bancos de dados da Web e Business e como atualizar um servidor V11 migrando seus bancos de dados diretamente para um pool de banco de dados elástico usando o PowerShell."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="sstein"/>

# Atualizar para o Banco de Dados SQL V12 usando o PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


O V12 de banco de dados SQL é a versão mais recente para atualizar para o V12 do banco de dados do SQL que é recomendado. O V12 do banco de dados SQL tem muitas [vantagens em relação à versão anterior](sql-database-v12-whats-new.md) incluindo:

- Maior compatibilidade com o SQL Server.
- Desempenho premium melhorado e novos níveis de desempenho.
- [Pools de banco de dados elásticos](sql-database-elastic-pool.md).

Esse artigo fornece instruções para atualizar os servidores existentes V11 de banco de dados SQL e bancos de dados para V12 de banco de dados SQL.

Durante o processo de atualização para V12 você vai atualizar qualquer banco de dados da Web e Business para uma nova camada de serviço para que instruções atualizem o bancos de dados da Web e Business que estão incluídos.

Além disso, migrar para um [pool de banco de dados elástico](sql-database-elastic-pool.md) pode ser mais econômico do que a atualização para níveis de desempenho individual (faixas de preço) para bancos de dados únicos. Os pools também simplificam o gerenciamento de banco de dados porque você só precisa gerenciar as configurações de desempenho para o pool em vez de gerenciar separadamente os níveis de desempenho de bancos de dados individuais. Se você tiver bancos de dados em vários servidores, considere movê-los para o mesmo servidor e aproveitando para colocá-los em um pool.

Você pode facilmente migrar automaticamente dos servidores V11 diretamente para os pools de banco de dados elásticos executando as etapas neste artigo.

Observe que os bancos de dados permanecerão online e continuarão a trabalhar em toda a operação de atualização. No momento da transição real para o novo nível de desempenho temporário podem ocorrer quedas das conexões ao banco de dados com duração muito curtas que é normalmente em torno de 90 segundos, mas pode ser até 5 minutos. Se seu aplicativo [tiver um tratamento de falha transitória para encerramentos de conexão](sql-database-connect-central-recommendations.md), isso será suficiente para proteger contra a queda de conexões ao final da atualização.

A atualização para o V12 do banco de dados SQL não poderá ser desfeita. Após uma atualização, o servidor não pode ser revertido para V11.

Após a atualização para V12, as [recomendações de nível de serviço](sql-database-service-tier-advisor.md) e [as recomendações de pool elástico](sql-database-elastic-pool-create-portal.md) não estarão imediatamente disponíveis até que o serviço tenha tempo para avaliar suas cargas de trabalho no novo servidor. O histórico de recomendação do servidor V11 não se aplica ao servidores V12, portanto não é mantido.

## Prepare-se para atualizar

- **Atualizar todos os bancos de dados da Web e Business**: confira a seção [Atualizar todos os bancos de dados da Web e Business](sql-database-v12-upgrade.md#upgrade-all-web-and-business-databases) abaixo ou use o [PowerShell para atualizar bancos de dados e o servidor](sql-database-upgrade-server-powershell.md).
- **Examinar e suspender a replicação geográfica:** se o seu Banco de Dados SQL do Azure estiver configurado para replicação geográfica, você deverá documentar sua configuração atual e [parar a replicação geográfica](sql-database-geo-replication-portal.md#remove-secondary-database). Após a conclusão da atualização, você deve reconfigurar seu banco de dados para replicação geográfica.
- **Abra estas portas se você tiver clientes em uma VM do Azure**: se o seu programa cliente se conectar ao Banco de Dados SQL V12 enquanto seu cliente for executado em uma máquina virtual do Azure (VM), abra os seguintes intervalos de porta 11000-11999 e 14000-14999 na VM. Para obter detalhes, confira [Portas para o Banco de Dados SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Pré-requisitos

Para atualizar um servidor para a V12 com o PowerShell, você precisa ter o Azure PowerShell instalado e em execução e, dependendo da versão, talvez seja preciso alterná-lo para o modo de gerenciador de recursos, a fim de acessar os Cmdlets do PowerShell do Gerenciador de Recursos do Azure.

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).


## Configurar suas credenciais e selecionar sua assinatura

Para executar os cmdlets do PowerShell em sua assinatura do Azure, primeiramente você deve estabelecer acesso à sua conta do Azure. Execute o seguinte e você receberá um sinal na tela para inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

	Add-AzureRmAccount

Após entrar, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.

Para selecionar a assinatura com a qual deseja trabalhar, você precisará da ID (**-SubscriptionId**) ou do nome da assinatura (**-SubscriptionName**). Você pode copiar essas informações da etapa anterior ou, se tiver várias assinaturas, poderá executar o cmdlet **Get-AzureRmSubscription** e copiar as informações da assinatura desejada do conjunto de resultados.

Execute o cmdlet a seguir com as informações da sua assinatura para definir a assinatura atual:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Os comandos a seguir serão executados na assinatura que você acabou de selecionar acima.

## Obter recomendações

Para obter a recomendação para a atualização do servidor, execute o seguinte cmdlet:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Para obter mais informações, veja [Criar um pool de banco de dados elástico](sql-database-elastic-pool-create-portal.md) e [Recomendações sobre tipo de preço do Banco de Dados SQL do Azure](sql-database-service-tier-advisor.md).



## Inicie a atualização

Para iniciar a atualização do servidor, execute o seguinte cmdlet:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Quando você executar este comando, o processo de atualização será iniciado. Você pode personalizar a saída da recomendação e fornecer a recomendação editada para esse cmdlet.


## Atualizar um servidor


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Mapeamento de atualização personalizada

Se as recomendações não forem apropriadas para seu servidor e caso comercial, você poderá escolher o modo como seus bancos de dados são atualizados e mapeá-los a bancos de dados simples ou elásticos.

Os parâmetros ElasticPoolCollection e DatabaseCollection são opcionais:

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## Monitorar bancos de dados após a atualização para V12 do banco de dados SQL


Após a atualização, é recomendável monitorar o banco de dados ativamente para garantir que aplicativos estão sendo executados no desempenho esperado e uso ideal conforme necessário.

Além de monitorar os bancos de dados individuais, você pode monitorar os pools de banco de dados elástico [usando o portal](sql-database-elastic-pool-manage-portal.md) ou com o [PowerShell](sql-database-elastic-pool-manage-powershell.md)


**Dados de consumo de recursos:** para recursos de banco de dados Básico, Standard e Premium, os dados de consumo estão disponíveis através do DMV [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) no banco de dados do usuário. Essa DMV oferece, quase em tempo real, informações sobre consumo de recurso na granularidade a 15 segundos para a hora da operação anterior. O consumo de percentual de DTU para um intervalo é calculado como o consumo de percentual máxima das dimensões de CPU, E/S e log. Aqui está uma consulta para calcular o consumo médio de porcentagem de DTU na última hora:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informações adicionais de monitoramento:

- [Diretrizes de desempenho do Banco de Dados SQL do Azure para bancos de dados individuais](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).
- [Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)



**Alertas:** configurar “Alertas” no Portal do Azure para ser notificado quando o consumo de DTU de um banco de dados atualizado aproximar-se de certo nível alto. Alertas de banco de dados podem ser configurados no Portal do Azure para várias métricas de desempenho como Log, CPU, E/S e DTU. Navegue até o banco de dados e selecione **Regras de alerta** na folha **Configurações**.

Por exemplo, você pode configurar um alerta por email sobre “Porcentagem DTU” se o valor da porcentagem média de DTU exceder 75% nos últimos 5 minutos. Confira [Receber notificações de alerta](../azure-portal/insights-receive-alert-notifications.md) para saber mais sobre como configurar notificações de alerta.



## Próximas etapas

- [Criar um pool de banco de dados elástico](sql-database-elastic-pool-create-portal.md) e adicionar alguns ou todos os bancos de dados ao pool.
- [Alterar a camada de serviço e o nível de desempenho do banco de dados](sql-database-scale-up.md).



## Informações relacionadas

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=AcomDC_0330_2016-->