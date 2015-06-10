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
   ms.date="04/29/2015"
   ms.author="adamkr; sstein"/>

# Criar e gerenciar um pool elástico de Banco de Dados SQL com o PowerShell (visualização)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Visão geral

Este artigo mostra como criar e gerenciar um pool elástico de Banco de Dados SQL usando o PowerShell.


As etapas individuais para criar um pool elástico com o Azure PowerShell são divididas e explicadas por proporcionar clareza. Para quem deseja apenas uma lista concisa de comandos, confira a seção **Juntando as peças** ao final deste artigo.

Este artigo mostrará como criar tudo o que você precisa para criar e configurar um pool elástico, exceto para a assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.

> [AZURE.NOTE]No momento, os pools elásticos estão em versão de visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL.


## Pré-requisitos

Para criar um pool elástico com o PowerShell, o PowerShell no Azure precisar estar instalado e executando, e alterná-lo para o modo do gerenciador de recursos a fim de acessar os Cmdlets do PowerShell do Gerenciador de Recursos do Azure.

Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](powershell-install-configure.md).

Os cmdlets para criação e gerenciamento de Bancos de Dados SQL do Azure e pools elásticos estão localizados no módulo do Gerenciador de Recursos do Azure. Quando você inicia o PowerShell do Azure, os cmdlets no módulo do Azure são importados por padrão. Para alternar para o módulo do Gerenciador de Recursos do Azure, use o cmdlet Switch-AzureMode.

	PS C:>Switch-AzureMode -Name AzureResourceManager

Para obter informações detalhadas, confira [Usando o Windows PowerShell com o Gerenciador de Recursos](powershell-azure-resource-manager.md).


## Configurar suas credenciais e selecionar sua assinatura

Agora que você está executando o módulo do Gerenciador de Recursos do Azure, terá acesso a todos os cmdlets necessários para criar e configurar um pool elástico. Primeiro você deve estabelecer o acesso à sua conta do Azure. Execute o seguinte e você receberá um sinal na tela para inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

	PS C:>Add-AzureAccount

Após entrar, você verá algumas informações na tela, incluindo a ID usada para entrar e as assinaturas do Azure as quais você tem acesso.


### Selecionar sua assinatura do Azure

Para selecionar a assinatura é necessário ter a ID ou o nome da assinatura (**-SubscriptionName* *). Você pode copiar essa informação da etapa anterior, ou, se você tiver várias assinaturas, poderá executar o cmdlet **Get-Subscription** e copiar as informações da assinatura desejada do resultado. Quando tiver sua assinatura, execute o seguinte cmdlet:

	PS C:>Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Criar um grupo de recursos, servidor e regra de firewall

Agora que você tem acesso para executar cmdlets em sua assinatura do Azure, a próxima etapa é estabelecer o grupo de recursos que contém o servidor no qual o pool elástico será criado. Você pode editar o próximo comando a fim de usar qualquer local válido de sua escolha. Execute **(Get-AzureLocation | where-object {$_.Name -eq "Microsoft.Sql/servers" }).Locations** para obter uma lista de locais válidos.

Se você já tiver um grupo de recursos, poderá ir até a próxima etapa ou executar o comando a seguir para criar um novo grupo de recursos:

	PS C:>New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### Criar um servidor 

Os pools elásticos são criados dentro de Servidores SQL do Azure. Se você já tiver um servidor, vá para a próxima etapa ou execute o seguinte comando para criar um novo servidor V12. Substitua ServerName pelo nome do servidor. Esse nome deve ser exclusivo para Servidores do SQL do Azure. Você poderá receber um erro se o nome do servidor já existir. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído. Os detalhes do servidor e o prompt do PowerShell serão exibidos após a criação do servidor. Você pode editar o comando a fim de usar qualquer local válido de sua escolha.

	PS C:>New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Ao executar esse comando, uma janela é aberta solicitando um **Nome de usuário** e **Senha**. Essas não são suas credenciais do Azure, insira o nome de usuário e a senha que serão as credenciais de administrador que você deseja criar para o novo servidor.


### Configurar uma regra de firewall de servidor para permitir o acesso ao servidor

Estabeleça uma regra de firewall para acessar o servidor. Execute o comando a seguir substituindo os endereços IP inicial e final pelos valores válidos para o seu computador.

Se o servidor precisar permitir o acesso a outros serviços do Azure, adicione a opção **- AllowAllAzureIPs** que adicionará uma regra de firewall especial e permitirá que todo o tráfego do azure acesse o servidor.

	PS C:>New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Para saber mais, confira [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Criar um pool elástico e bancos de dados elásticos

Agora que você tem um grupo de recursos, um servidor e uma regra de firewall configurados para poder acessar o servidor, o comando a seguir criará o pool elástico. Esse comando cria um pool que compartilha um total de 400 DTUs. É garantido que cada banco de dados no pool terá sempre 10 DTUs disponíveis (DatabaseDtuMin). Os bancos de dados individuais no pool podem consumir no máximo 100 DTUs (DatabaseDtuMax). Para obter explicações detalhadas sobre o parâmetro, confira [Pools elásticos do Banco de Dados SQL do Azure](sql-database-elastic-pool.md).


	PS C:>New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Criar ou adicionar bancos de dados elásticos em um pool

O pool elástico criado na etapa anterior está vazio, ele não contém bancos de dados. As seções a seguir mostram como criar novos bancos de dados dentro do pool elástico e também como adicionar bancos de dados existentes ao pool.


### Criar um novo banco de dados elástico dentro de um pool elástico

Para criar um novo banco de dados diretamente dentro de um pool elástico, use o cmdlet **New-AzureSqlDatabase** e defina o parâmetro **ElasticPoolName**.


	PS C:>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Mover um banco de dados existente para um pool elástico

Para mover um banco de dados existente para um pool elástico, use o cmdlet **AzurSqlDatabase conjunto** e defina o parâmetro **ElasticPoolName**.


Para a demonstração, crie um banco de dados que não esteja em um pool elástico.

	PS C:>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Mova o banco de dados existente para o pool elástico.

	PS C:>Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Monitorando bancos de dados elásticos e pools elásticos

### Obter o status das operações de pool elástico

Você pode acompanhar o status das operações de pool elástico, incluindo a criação e atualizações.

	PS C:> Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Obter o status de movimentação de um banco de dados elástico para dentro e fora de um pool elástico

	PS C:>Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Obter métricas do consumo de recurso de um pool elástico

Métricas que podem ser recuperadas como uma porcentagem do limite do pool de recursos:

* Utilização média da CPU - cpu_percent 
* Utilização média de ES - data_io_percent 
* Utilização média de log - log_write_percent 
* Utilização média da memória - memory_percent 
* Utilização média de DTU (como um valor máximo de utilização de CPU/ES/Log) – DTU_percent 
* Número máximo de solicitações de usuário simultâneas (operadores) – max_concurrent_requests 
* Número máximo de sessões de usuário simultâneas – max_concurrent_sessions 
* Tamanho total de armazenamento para o pool elástico – storage_in_megabytes 


Períodos de retenção/granularidade das métricas:

* Os dados retornarão com uma granularidade de cinco minutos.  
* A retenção de dados é de 14 dias.  


Esse cmdlet e a API limitam o número de linhas que podem ser recuperadas em uma chamada para 1000 linhas (cerca de três dias de dados a uma granularidade de cinco minutos). Mas esse comando pode ser chamado várias vezes com intervalos de tempo de início/término diferentes para recuperar mais dados


Recupere as métricas:

	PS C:> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Obtenha mais dias repetindo a chamada e acrescentando os dados:

	PS C:> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formate a tabela:

    PS C:> $table = Format-MetricsAsTable $metrics 

Exporte para um arquivo CSV:

    PS C:> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Obter métricas do consumo de recursos de um banco de dados elástico

Essas APIs são as mesmas que as APIs (V12) atuais usadas para monitorar a utilização de recursos de um banco de dados autônomo, exceto para as seguintes diferenças semânticas

* Para essa API as métricas recuperadas são expressas como uma porcentagem do databaseDtuMax (ou limite equivalente para a métrica subjacente, como CPU, ES etc) definido para esse pool elástico. Por exemplo, 50% da utilização de qualquer uma dessas métricas indica que o consumo do recursos específico é de 50% do limite por banco de dados desse recurso no pool elástico pai. 

Obtenha as métricas: PS C:> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

Obtenha mais dias se for necessário repetindo a chamada e acrescentando os dados:

    PS C:> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formate a tabela:

    PS C:> $table = Format-MetricsAsTable $metrics 

Exporte para um arquivo CSV:

    PS C:> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Juntando as peças


    Switch-AzureMode -Name AzureResourceManager
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Próximas etapas

Depois de criar um pool elástico, você pode gerenciar os bancos de dados elásticos no pool criando trabalhos elásticos. Trabalhos elásticos facilitam a execução de scripts T-SQL em qualquer quantidade de bancos de dados no pool.

Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).


## Referência de banco de dados elástico

Para saber mais sobre pools de banco de dados elásticos e bancos de dados elásticos, incluindo detalhes sobre APIs e erros, confira a [Referência de bancos de dados elásticos](sql-database-elastic-pool-reference.md).

<!---HONumber=58-->