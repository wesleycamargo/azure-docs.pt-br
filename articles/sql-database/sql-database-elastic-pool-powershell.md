<properties 
   pageTitle="Criar e gerenciar um pool elástico de bancos de dados de Banco de Dados SQL usando PowerShell" 
   description="Criar e gerenciar um pool elástico de bancos de dados de Banco de Dados SQL do Azure usando PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="09/23/2015"
   ms.author="adamkr; sstein"/>

# Criar e gerenciar um pool elástico de bancos de dados de Banco de Dados SQL usando PowerShell

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Visão geral

Este artigo mostra como criar e gerenciar um pool de banco de dados elástico do Banco de Dados SQL usando o PowerShell.


As etapas individuais para criar um pool de banco de dados elástico com o Azure PowerShell são divididas e explicadas por proporcionar clareza. Para quem deseja apenas uma lista concisa de comandos, confira a seção **Juntando as peças** ao final deste artigo.

Este artigo mostrará como criar tudo o que você precisa para criar e configurar um pool de banco de dados elástico, exceto pela assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.

> [AZURE.NOTE]No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com servidores V12 do Banco de Dados SQL.


## Pré-requisitos

Para criar um pool de banco de dados elástico com o PowerShell, você deve ter o Azure PowerShell instalado e em execução e colocá-lo no modo de gerenciador de recursos para acessar os Cmdlets do PowerShell do Gerenciador de Recursos do Azure.

Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).

Os cmdlets para criação e gerenciamento de bancos de dados SQL do Azure e pools de banco de dados elástico estão localizados no módulo do Gerenciador de Recursos do Azure. Quando você inicia o PowerShell do Azure, os cmdlets no módulo do Azure são importados por padrão. Para alternar para o módulo do Gerenciador de Recursos do Azure, use o cmdlet Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Para obter informações detalhadas, confira [Usando o Windows PowerShell com o Gerenciador de Recursos](powershell-azure-resource-manager.md).


## Configurar suas credenciais e selecionar sua assinatura

Agora que você está executando o módulo do Gerenciador de Recursos do Azure, terá acesso a todos os cmdlets necessários para criar e configurar um pool de banco de dados elástico. Primeiro você deve estabelecer o acesso à sua conta do Azure. Execute o seguinte e você receberá um sinal na tela para inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

	Add-AzureAccount

Após entrar, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura é necessário ter a ID ou o nome da assinatura (**-SubscriptionName**). Você pode copiar essas informações da etapa anterior, ou, se tiver várias assinaturas, poderá executar o cmdlet **Get-AzureSubscription** e copiar as informações da assinatura desejada do resultado. Quando tiver sua assinatura, execute o seguinte cmdlet:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Criar um grupo de recursos, servidor e regra de firewall

Agora que você tem acesso para executar cmdlets em sua assinatura do Azure, a próxima etapa é estabelecer o grupo de recursos que contém o servidor no qual o pool de banco de dados elástico será criado. Você pode editar o próximo comando a fim de usar qualquer local válido de sua escolha. Execute **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** para obter uma lista de locais válidos.

Se você já tiver um grupo de recursos, poderá ir até a próxima etapa ou executar o comando a seguir para criar um novo grupo de recursos:

	New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### Criar um servidor 

Os pools de banco de dados elástico são criados nos servidores do Banco de Dados SQL do Azure. Se você já tiver um servidor, vá para a próxima etapa ou execute o seguinte comando para criar um novo servidor V12. Substitua ServerName pelo nome do servidor. Esse nome deve ser exclusivo para Servidores do SQL do Azure. Você poderá receber um erro se o nome do servidor já existir. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído. Os detalhes do servidor e o prompt do PowerShell serão exibidos após a criação do servidor. Você pode editar o comando a fim de usar qualquer local válido de sua escolha.

	New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Ao executar esse comando, uma janela é aberta solicitando um **Nome de usuário** e **Senha**. Essas não são suas credenciais do Azure, insira o nome de usuário e a senha que serão as credenciais de administrador que você deseja criar para o novo servidor.


### Configurar uma regra de firewall de servidor para permitir o acesso ao servidor

Estabeleça uma regra de firewall para acessar o servidor. Execute o comando a seguir substituindo os endereços IP inicial e final pelos valores válidos para o seu computador.

Se o servidor precisar permitir o acesso a outros serviços do Azure, adicione a opção **- AllowAllAzureIPs** que adicionará uma regra de firewall especial e permitirá que todo o tráfego do azure acesse o servidor.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Criar um pool de banco de dados elástico e bancos de dados elásticos

Agora que você tem um grupo de recursos, um servidor e uma regra de firewall configurados, é possível acessar o servidor. O comando a seguir criará o pool de banco de dados elástico. Esse comando cria um pool que compartilha um total de 400 eDTUs. É garantido que cada banco de dados no pool terá sempre 10 eDTUs disponíveis (DatabaseDtuMin). Os bancos de dados individuais no pool podem consumir no máximo 100 eDTUs (DatabaseDtuMax). Para obter explicações detalhadas sobre o parâmetro, confira [Pools elásticos do Banco de Dados SQL do Azure](sql-database-elastic-pool.md).


	New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Criar ou adicionar bancos de dados elásticos em um pool de banco de dados elástico

O pool criado na etapa anterior está vazio, ele não contém bancos de dados elásticos. As seções a seguir mostram como criar novos bancos de dados elásticos dentro do pool e também como adicionar bancos de dados existentes ao pool.

*Depois de criar um pool você também pode usar o Transact-SQL para criar um novo banco de dados elástico no pool, e mover bancos de dados existentes dentro e fora de um pool. Para saber mais detalhes, consulte [Referência de pool de banco de dados elástico - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

### Criar um novo banco de dados elástico dentro de um pool de banco de dados elástico

Para criar um novo banco de dados diretamente dentro de um pool, use o cmdlet **New-AzureSqlDatabase** e defina o parâmetro **ElasticPoolName**.


	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Mover um banco de dados existente para um pool de banco de dados elástico

Para mover um banco de dados existente para um pool, use o cmdlet **Set-AzureSqlDatabase** e defina o parâmetro **ElasticPoolName**.


Para a demonstração, crie um banco de dados que não esteja em um pool de banco de dados elástico.

	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Mova o banco de dados existente para o pool de banco de dados elástico.

	Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Alterar as configurações de desempenho de um pool de banco de dados elástico


    Set-AzureSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Monitorando bancos de dados elásticos e pools de banco de dados elástico

### Obter o status de operações do pool de banco de dados elástico

Você pode acompanhar o status das operações de pool de banco de dados elástico, incluindo a criação e as atualizações.

	Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Obter o status de mover um banco de dados elástico para dentro e fora de um pool de banco de dados elástico

	Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Obter métricas de consumo de recursos para um pool de banco de dados elástico

Métricas que podem ser recuperadas como uma porcentagem do limite do pool de recursos:

* Utilização média da CPU - cpu\_percent 
* Utilização média de ES - data\_io\_percent 
* Utilização média de log - log\_write\_percent 
* Utilização média da memória - memory\_percent 
* Utilização média de eDTU (como um valor máximo de utilização de CPU/ES/Log) – DTU\_percent 
* Número máximo de solicitações de usuário simultâneas (operadores) – max\_concurrent\_requests 
* Número máximo de sessões de usuário simultâneas – max\_concurrent\_sessions 
* Tamanho total de armazenamento para o pool elástico – storage\_in\_megabytes 


Períodos de retenção/granularidade das métricas:

* Os dados retornarão com uma granularidade de cinco minutos.  
* A retenção de dados é de 14 dias.  


Esse cmdlet e a API limitam o número de linhas que podem ser recuperadas em uma chamada para 1000 linhas (cerca de três dias de dados a uma granularidade de cinco minutos). Mas esse comando pode ser chamado várias vezes com intervalos de tempo de início/término diferentes para recuperar mais dados


Recupere as métricas:

	$metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Obtenha mais dias repetindo a chamada e acrescentando os dados:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formate a tabela:

    $table = Format-MetricsAsTable $metrics 

Exporte para um arquivo CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Obter métricas do consumo de recursos de um banco de dados elástico

Essas APIs são as mesmas que as APIs (V12) atuais usadas para monitorar a utilização de recursos de um banco de dados autônomo, exceto para as seguintes diferenças semânticas

* Para essa API as métricas recuperadas são expressas como um percentual do databaseDtuMax (ou limite equivalente para a métrica subjacente, como CPU, ES etc) definido para esse pool de banco de dados elástico. Por exemplo, 50% da utilização de qualquer uma dessas métricas indica que o consumo do recursos específico é de 50% do limite por banco de dados desse recurso no pool de banco de dados elástico pai. 

Obtenha as métricas: $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

Obtenha mais dias se for necessário repetindo a chamada e acrescentando os dados:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formate a tabela:

    $table = Format-MetricsAsTable $metrics 

Exporte para um arquivo CSV:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Juntando as peças


    Switch-AzureMode -Name AzureResourceManager
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Próximas etapas

Depois de criar um pool de banco de dados elástico, você pode gerenciar os bancos de dados elásticos no pool criando trabalhos elásticos. Trabalhos elásticos facilitam a execução de scripts T-SQL em qualquer quantidade de bancos de dados no pool. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).


## Referência de banco de dados elástico

Para saber mais sobre pools de banco de dados elásticos e bancos de dados elásticos, incluindo detalhes sobre APIs e erros, confira a [Referência de bancos de dados elásticos](sql-database-elastic-pool-reference.md).

<!---HONumber=Oct15_HO1-->