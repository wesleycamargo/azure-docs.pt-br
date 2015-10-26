<properties 
	pageTitle="Atualizar um Azure SQL Server para o V12 com o PowerShell" 
	description="Atualize um Azure SQL Server para o V12 com o PowerShell." 
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
	ms.date="10/08/2015" 
	ms.author="sstein"/>

# Atualização para o Banco de Dados SQL V12 com o PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


Este artigo mostra como atualizar para o Banco de Dados SQL V12 com o PowerShell.

Durante o processo de atualização para o Banco de Dados SQL V12, você também deverá atualizar todos os bancos de dados da Web e Empresariais para uma nova camada de serviço. As instruções a seguir incluem o uso de recomendações de tipo de preço e pool elástico para ajudá-lo a [atualizar qualquer banco de dados da Web e Empresarial](sql-database-upgrade-new-service-tiers.md) no servidor.


## Pré-requisitos 

Para atualizar um servidor para a V12 com o PowerShell, você precisa ter o Azure PowerShell instalado e em execução e, dependendo da versão, talvez seja preciso alterná-lo para o modo de gerenciador de recursos, a fim de acessar os Cmdlets do PowerShell do Gerenciador de Recursos do Azure.

> [AZURE.IMPORTANT]A partir da liberação da Visualização do Azure PowerShell 1.0, o cmdlet Switch-AzureMode não está mais disponível, e os cmdlets contidos no módulo do Gerenciador de Recursos do Azure foram renomeados. Os exemplos neste artigo usam a nova convenção de nomenclatura do PowerShell 1.0 Preview. Para obter informações detalhadas, veja [Substituição de Switch-AzureMode no Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução, e devido à remoção de Switch-AzureMode, você deve baixar e instalar o Azure PowerShell mais recente executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).


## Configurar suas credenciais e selecionar sua assinatura

Para executar os cmdlets do PowerShell em sua assinatura do Azure, primeiramente você deve estabelecer acesso à sua conta do Azure. Execute o seguinte e você receberá um sinal na tela para inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

	Add-AzureAccount

Após entrar, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.

Para selecionar a assinatura com a qual deseja trabalhar, você precisa da ID (**-SubscriptionId**) ou do nome da assinatura (**-SubscriptionName**). Você pode copiar essas informações da etapa anterior, ou, se tiver várias assinaturas, poderá executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do resultado.

Execute o cmdlet a seguir com as informações da sua assinatura para definir a assinatura atual:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Os comandos a seguir serão executados na assinatura que você acabou de selecionar acima.

## Obter recomendações

Para obter a recomendação para a atualização do servidor, execute o seguinte cmdlet:

    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Para obter mais informações, confira [as recomendações sobre pool de banco de dados elástico do Banco de Dados SQL do Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) e [as recomendações sobre tipo de preço do Banco de Dados SQL do Azure](sql-database-service-tier-advisor.md).



## Inicie a atualização

Para iniciar a atualização do servidor, execute o seguinte cmdlet:

    Start-AzureRMSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Quando você executar este comando, o processo de atualização será iniciado. Você pode personalizar a saída da recomendação e fornecer a recomendação editada para esse cmdlet.


## Atualizar um servidor


    # Adding the account
    #
    Add-AzureAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRMSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


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
    Start-AzureRMSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    




## Informações relacionadas

- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=Oct15_HO3-->