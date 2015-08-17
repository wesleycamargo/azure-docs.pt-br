<properties 
	pageTitle="Considerações de preço e desempenho para um pool de banco de dados elástico | Pools de banco de dados elástico do Banco de Dados SQL do Azure" 
	description="Um pool de bancos de dados elástico é um conjunto de recursos disponíveis compartilhados por um grupo de bancos de dados elásticos. Este documento fornece diretrizes para ajudar a avaliar a adequação do uso de um pool de banco de dados elástico para um grupo de bancos de dados." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/24/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Considerações de preço e desempenho para um pool de banco de dados elástico


Este documento fornece diretrizes para ajudar a avaliar a economia de usar um pool de banco de dados elástico para um grupo de bancos de dados com base em padrões de uso do banco de dados e diferenças de preços entre um pool elástico e bancos de dados individuais. Diretrizes adicionais também são fornecidas para ajudar a determinar o tamanho do pool atual necessário para um conjunto existente de Bancos de Dados SQL.

- Para ter uma visão geral dos pools de banco de dados elástico, consulte [Pools banco de dados do banco elástico do Banco de Dados SQL](sql-database-elastic-pool.md).
- Para obter informações detalhadas sobre os pools de banco de dados elástico, consulte [Referência do pool de banco de dados elástico do Banco de Dados SQL](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]No momento, os pools elásticos estão em versão de visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL.

## Pools de banco de dados elástico

ISVs de SaaS desenvolvem aplicativos criados com base em camadas de dados de grande escala compostas por vários bancos de dados. Um padrão comum de aplicativo para cada um desses bancos de dados é ter diferentes clientes com padrões de uso exclusivamente variados e imprevisíveis. Pode ser difícil para o ISV prever os requisitos de recursos de cada banco de dados individualmente. Em tais circunstâncias, o ISV pode provisionar excessivamente os recursos causando despesas consideráveis para garantir que uma taxa de transferência e tempos de resposta favoráveis para todos os bancos de dados. Ou então, o ISV pode gastar menos com o risco de oferecer uma experiência de baixo desempenho para seus clientes.

Pools de banco de dados elástico no Banco de Dados SQL do Azure permitem que ISVs de SaaS otimizem o desempenho de preço para um grupo de bancos de dados dentro de um orçamento prescrito oferecendo elasticidade de desempenho para cada banco de dados. Pools elásticos permitem ao ISV adquirir DTUs (Taxa de Transferência de Banco de Dados) para um pool elástico compartilhado por vários bancos de dados para acomodar períodos de uso imprevisíveis em bancos de dados individuais. O requisito de DTU para um pool elástico é determinado pela utilização de agregação de seus bancos de dados. A quantidade de DTUs disponíveis para o pool elástico é controlado pelo orçamento do ISV. Pools elásticos facilitam a tarefa de ISVs ponderarem sobre o impacto do orçamento no desempenho e vice-versa para o pool. Basta o ISV adicionar bancos de dados ao pool elástico, definir as garantias ou limites de DTU necessários para os bancos de dados e definir a DTU do pool com base no seu orçamento. Usando pools elásticos, os ISVs podem aumentar seu serviço com perfeição desde uma startup lean até uma empresa madura em escala cada vez maior.
  


## Quando considerar um pool de banco de dados elástico

Pools elásticos também são indicados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por baixa utilização média com picos de utilização relativamente pouco frequentes.

Quanto mais bancos de dados forem adicionados ao pool, maior será a economia, porém, dependendo do seu padrão de utilização do aplicativo, é possível observar economia em um mínimo de quatro bancos de dados S3.

As seções a seguir ajudarão a compreender como avaliar se sua coleção específica de bancos de dados se beneficiará com o uso de um pool elástico.

### Avaliar os padrões de utilização do banco de dados

A figura a seguir mostra um exemplo de um banco de dados que passa muito tempo ocioso, mas também apresenta picos de atividade periodicamente. Este é um padrão de utilização bastante adequado para um pool elástico:
 
   ![um banco de dados][1]

Para o período de uma hora ilustrado acima, DB1 apresenta picos de até 90 DTUs, mas seu uso geral médio é inferior a 5 DTUs. Um nível de desempenho S3 é exigido para executar essa carga de trabalho em um banco de dados individual. Contudo, isso deixa a maioria dos recursos sem utilização durante períodos de baixa atividade.

Um pool elástico permite que essas DTUs não utilizadas sejam compartilhadas entre vários bancos de dados, reduzindo a quantidade total de DTUs necessárias e os custos gerais.

Considerando ainda o exemplo anterior, suponha que há outros bancos de dados com padrões de utilização semelhantes ao do DB1. Nas próximas duas figuras abaixo, a utilização de quatro e 20 bancos de dados é disposta no mesmo gráfico para ilustrar como sua utilização não se sobrepõe ao longo do tempo:

   ![quatro banco de dados][2]

   ![vinte bancos de dados][3]

A utilização de DTU agregada em todos os 20 bancos de dados é ilustrada pela linha preta na figura acima. Ela mostra que a utilização de DTU agregada nunca excede 100 DTUs e indica que os 20 bancos de dados podem compartilhar 100 DTUs durante esse período de tempo. Isso resulta em uma economia de 20x de DTUs e 6x no preço em comparação com a colocação de cada um dos bancos de dados nos níveis de desempenho S3 para bancos de dados individuais.


Este exemplo é ideal pelas seguintes razões:

- Há grandes diferenças entre o pico de utilização e a utilização média por banco de dados.  
- O pico de utilização de cada banco de dados ocorre em diferentes momentos. 
- DTUs são compartilhados entre vários bancos de dados.


O preço de um pool elástico é uma função do pool de DTUs e o número de bancos de dados nele presentes. Embora o preço unitário de DTU para um pool considerando preços de GA seja três vezes maior que o preço unitário de DTU para um banco de dados individual, as DTUs em pool podem ser compartilhadas por vários bancos de dados, e em muitos casos menos DTUs são necessárias no total. Essas distinções no preço e compartilhamento de DTU são a base do potencial de economia que os pools podem oferecer.

<br>

As seguintes regras básicas relacionadas à contagem e utilização de banco de dados ajudam a garantir que um pool elástico ofereça um custo reduzido em comparação ao uso de níveis de desempenho de bancos de dados individuais. As diretrizes são baseadas nos preços de disponibilidade geral (GA). Observe que os preços de GA tem desconto de 50% durante a visualização, por isso essas regras gerais devem ser consideradas relativamente moderadas.


### Número mínimo de bancos de dados

Com preços de GA, um pool elástico torna-se uma opção mais econômica de desempenho se 1 DTU puder ser compartilhada por mais de três bancos de dados. Isso significa que a soma de DTUs dos níveis de desempenho para bancos de dados individuais é mais de três vezes as DTUs do pool. Para ver os tamanhos disponíveis, consulte [Limites de DTU e armazenamento para pools e bancos de dados elásticos](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


***Exemplo***<br> pelo menos quatro bancos de dados S3 ou bancos de dados, pelo menos, 36 S0 são necessários para um pool de elástica DTU 100 ser mais econômica que usar níveis de desempenho para bancos de dados individuais. (Observe que com preços de visualização, o ponto de equilíbrio de preço com base na contagem de banco de dados fica reduzido a dois bancos de dados S3 ou 17 bancos de dados S0).



### Número máximo de banco de dados em pico simultaneamente

Ao compartilhar DTUs, nem todos os bancos de dados em um pool podem usar as DTUs simultaneamente até o limite disponível ao usar níveis de desempenho de bancos de dados individuais. Quanto menos bancos de dados em pico simultaneamente, menor poderá ser o DTU do pool e mais econômico ainda. Em geral, no máximo 1/3 dos bancos de dados no pool devem estar em pico simultaneamente em seus limites DTU.

***Exemplo***<br> Para reduzir os custos para quatro bancos de dados S3 em um pool com 200 DTU, no máximo dois desses bancos de dados pode estar simultaneamente no máximo de sua utilização. Caso contrário, se mais de dois desses quatro bancos de dados S3 entrarem em pico simultaneamente, o pool precisaria ser dimensionado para mais de 200 DTUs. E se o pool for redimensionado para mais de 200 DTUs,mais bancos de dados S3 precisarão ser adicionados ao pool para manter os custos menores do que os níveis de desempenho de bancos de dados individuais.


Observe que esse exemplo não considera a utilização de outros bancos de dados no pool. Se todos os bancos de dados tiverem certa utilização em um dado momento, menos que 1/3 dos bancos de dados deve usar o máximo simultaneamente.


### Utilização de DTU por banco de dados

Uma grande diferença entre o máximo e média de utilização de um banco de dados indica longos períodos de baixa utilização e curtos períodos de alta utilização. Esse padrão de utilização é ideal para compartilhar recursos entre bancos de dados. Um banco de dados deve ser considerado para um pool quando sua utilização máxima for aproximadamente três vezes maior que sua utilização média.

    
***Exemplo***<br> Um banco de dados S3 com picos de 100 DTUs que usa em média 30 DTUs ou menos é um bom candidato para o compartilhamento de DTUs em um pool elástico. Outra opção de bom candidato para um pool elástico seria um banco de dados S1 que com pico de 20 DTUs e média de uso se sete DTUs.
    

## Heurística para comparar a diferença de preços entre um pool elástico e bancos de dados individuais 

A heurística a seguir pode ajudar a estimar se um pool elástico é mais econômico do que usar bancos de dados individuais.

1. Estime as DTUs necessárias para o pool da seguinte maneira:
    
    MÁX(**Número total de DBs* * *Utilização de DTU média por DB*, *número de DBs em pico simultaneamente* * *Pico de utilização de DTU por DB*)

2. Selecione o menor valor de DTU disponível para o pool que seja maior que a estimativa da etapa 1. Para ver as opções de DTU disponíveis, consulte os valores válidos para as DTUs listadas aqui: [Limites de DTU e armazenamento para pools e bancos de dados elásticos](sql-database-elastic-pool-reference.md#dtu-and-storage-limits-for-elastic-pools-and-elastic-databases).

    


3. Calcule o preço para o pool da seguinte maneira:

    preço do pool = (*DTUs do pool* * *preço unitário de DTU do pool*) + (*número total de DBs* * *preço unitário de DB do pool*)

    Consulte [Preços de Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/) para obter informações sobre preços.


4. Compare o preço do pool da etapa 3 com o preço de usar os níveis de desempenho adequados para bancos de dados individuais.



## Determinar o melhor tamanho DTU de pool para Bancos de Dados SQL existente 

O melhor tamanho de um pool elástico depende das DTUs e recursos de armazenamento agregados necessários para todos os bancos de dados no pool. Isso inclui determinar a maior das duas quantidades a seguir:

* DTUs máximo utilizadas por todos os bancos de dados no pool.
* Bytes de armazenamento máximo utilizados por todos os bancos de dados no pool. 

Observe que, para a camada de serviço Standard, 1 GB de armazenamento é alocado para cada 1 DTU configurada para o pool. Por exemplo, se um pool for configurado com 200 DTUs, seu limite de armazenamento será 200 GB.

### Use o STA (Supervisor de Camadas do Serviço) e DMVs (Exibições de Gerenciamento Dinâmico) para obter recomendações de dimensionamento   

O STA e DMVs fornecem diferentes opções de ferramentas e recursos para dimensionamento de um pool elástico. Independentemente da opção de ferramentas usada, a estimativa de dimensionamento deve ser usada somente como uma avaliação inicial e criação de pools elásticos. Depois de criar um pool elástico, seu uso de recursos deve ser monitorado com precisão e as configurações de desempenho do pool devem ser ajustadas conforme necessário.

**STA**<br>STA é uma ferramenta interna no Portal do Azure que avalia automaticamente a utilização histórica de recursos dos bancos de dados em um servidor de Banco de Dados SQL existente e recomenda uma configuração de pool elástico apropriada.

**Ferramenta de dimensionamento de DMV**<br>a ferramenta de dimensionamento de DMV é fornecida como um script do PowerShell e permite personalizar as estimativas de dimensionamento de um pool elástico para bancos de dados existentes em um servidor.

### Escolhendo entre as ferramentas STA e DMV 

Selecione a ferramenta apropriada para analisar seu aplicativo específico. A tabela a seguir resume as diferenças entre essas duas ferramentas de dimensionamento:

| Recurso | STA | DMVs |
| :--- | :--- | :--- |
| Granularidade de amostras de dados usada na análise. | 15 s | 15 s |
| Considera as diferenças de preço entre um pool e os níveis de desempenho para bancos de dados individuais. | Sim | Não |
| Permite personalizar a lista de bancos de dados analisados dentro de um servidor. | Não | Sim |
| Permite personalizar o período de tempo usado na análise. | Não | Sim |


### Estimar o tamanho do pool elástico usando STA  

O Azure avalia o histórico de utilização dos bancos de dados e recomenda um pool elástico quando ele é mais econômico do que o uso de níveis de desempenho para bancos de dados individuais. Se um pool elástico é recomendado, a ferramenta fornece uma lista de bancos de dados recomendados, bem como a quantidade recomendada de DTUs do pool e configurações de DTU mín/máx para cada banco de dados elástico. Para que um banco de dados seja considerado como candidato para um pool elástico, ele deve existir há pelo menos de 14 dias. A visualização pública do pool de banco de dados elástico é limitada a somente Standard, por isso bancos de dados Premium ainda não são considerados candidatos para um pool elástico.

O STA está disponível no portal do Azure ao adicionar um pool elástico a um servidor existente. Se as recomendações para um pool elástico estiverem disponíveis para o servidor, elas serão exibidas no folha de criação “Pool de Banco de Dados Elástico”. Os clientes sempre podem alterar as configurações recomendadas para criar seu próprio agrupamento de pool elástico.

### Estimar o tamanho do pool elástico usando DMVs (Exibições de Gerenciamento Dinâmico) 

A DMV [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) mede a utilização de recursos de um banco de dados individual. Essa DMV apresenta CPU, E/S, log e utilização de log do banco de dados como um percentual do limite de nível de desempenho do banco de dados. Esses dados podem ser usados para calcular a utilização de DTU de um banco de dados em qualquer intervalo de 15 segundos.

A utilização de DTU agregada de um pool elástico em um intervalo de 15 segundos pode ser estimada agregando a utilização de DTU para todos os bancos de dados candidatos durante esse período. Dependendo das metas de desempenho específicas, pode fazer sentido descartar um pequeno percentual dos dados de exemplo. Por exemplo, um valor de 99% das DTUs agregadas em todos os intervalos de tempo pode ser aplicado para excluir exceções e fornecer um DTU de pool elástico para adequar a 99% dos intervalos de tempo de exemplo.

## Script do PowerShell para calcular o uso de DTU agregado de bancos de dados

Um exemplo de script do PowerShell é fornecido aqui para estimar os valores agregados de DTU para bancos de dados do usuário em um servidor de Banco de Dados SQL.

O script só coleta dados quando está em execução. Para uma carga de trabalho de produção típica, você deve executar o script por pelo menos um dia, embora uma semana ou até mesmo mais provavelmente forneça uma estimativa mais precisa. Execute o script pelo período que representar sua carga de trabalho típica dos bancos de dados.

> [AZURE.IMPORTANT]Você deve manter a janela do PowerShell aberta durante a execução do script. Não feche a janela do PowerShell até executar o script por um período de tempo suficiente e capturar dados suficientes para representar sua carga de trabalho típica abrangendo ambos os períodos de uso normal e de pico.

### Pré-requisitos de script 

Instale o seguinte antes de executar o script:

- A versão mais recente das [ferramentas de linha de comando do Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- O [Feature Pack do SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).


### Detalhes do script


Você pode executar o script do seu computador local ou uma VM na nuvem. Ao executá-lo em seu computador local, você pode incorrer em encargos de saída de dados, pois o script precisa baixar dados de seus bancos de dados de destino. Veja abaixo a estimativa de volume de dados com base no número de bancos de dados de destino e a duração da execução do script. Para ver os custos de transferência de dados do Azure, consulte os [Detalhes de Preços de Transferência de Dados](http://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     Um banco de dados por hora = 38 KB
 -     Um banco de dados por dia = 900 KB
 -     Um banco de dados por semana = 6 MB
 -     100 bancos de dados por dia = 90 MB
 -     500 bancos de dados por semana = 3 GB

O script exclui determinados bancos de dados que não são bons candidatos para a oferta de visualização pública atual da camada de pool elástico Standard. Se você precisar excluir outros bancos de dados do servidor de destino e alterar o script para atender aos seus critérios. Por padrão, o script não compila informações para o seguinte:

* Todos os bancos de dados Premium em servidores V12.
* Bancos de dados P2 e P3 em servidores V11.
* Bancos de dados elásticos (bancos de dados que já estão em um pool elástico).
* Banco de dados mestre do servidor.

O script precisa de um banco de dados de saída para armazenar os dados intermediários para análise. Você pode usar um banco de dados novo ou existente. Embora tecnicamente não seja necessário para a execução da ferramenta, é recomendável que o banco de dados de saída esteja em um servidor diferente para evitar impactos no resultado da análise. Sugira que o nível de desempenho do banco de dados de saída seja pelo menos S0 ou superior. Durante a coleta de longa duração de dados para um grande número de bancos de dados, você pode considerar atualizar seu banco de dados de saída para um nível mais alto de desempenho.

O script precisa que você forneça as credenciais para conectar ao servidor de destino (o candidato ao pool de banco de dados elástico) com o nome completo do servidor como "abcdef.database.windows.net". Atualmente o script não dá suporte à análise de vários servidores simultaneamente.



Depois de enviar os valores para o conjunto inicial de parâmetros, você precisará fazer logon na sua conta do Azure. Isso é usado para fazer logon no servidor de destino, não no servidor de banco de dados de saída.
	
Se você encontrar os avisos a seguir ao executar o script, poderá ignorá-los:

- AVISO: O cmdlet Switch-AzureMode foi preterido.
- AVISO: Não foi possível obter informações do serviço do SQL Server. Falha ao tentar conectar ao WMI em “Microsoft.Azure.Commands.Sql.dll” com o seguinte erro. O servidor RPC está indisponível.

Quando o script for concluído, ele produzirá o número estimado de DTUs necessário para um pool elástico para conter todos os bancos de dados candidatos no servidor de destino. Este DTU estimado pode ser usado para criar e configurar um pool elástico para conter esses bancos de dados. Depois que o pool for criado e os bancos de dados forem movido para o pool, ele deve ser monitorado atentamente alguns dias e ajustes na configuração do pool de DTU devem ser feitos conforme necessário.


Para selecionar todo o script para copiar, clique em qualquer texto no script três vezes (clique triplo).

    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, Premium SKU (P1 in previous version still qualifies), and any databases that are already in a pool. You may add more to the excluded list below as needed
    if ($version -in ("12.0", "Completed")) # This is on Azure Database V12. 
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceLevelObjectiveName -notlike ("P*") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notlike ("P*")}
    }
    else # This is previous versions of Azure Database
    {
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool", "P2", "P3") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool", "P2", "P3")}
    }
    
    # Due to pricing model difference, we don't recommend to put less than 3 databases in a pool. 
    if ($ListOfDBs.Count -lt 3)
    {
    Write-Host "There are less than 3 qualified standalone databases in this server. Not a good candidate for Elastic Database Pool!" -ForegroundColor Red
    }
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
     foreach ($db in $ListOfDBs)
     {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100 -- for old version of SQL database, P1 has same performance capacity as S3.
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    #write-host $sql
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
     }
    
     $start_time = $start_time.AddMinutes($Waittime)
     $end_time = $end_time.AddMinutes($Waittime)
     Write-Host $start_time
     Write-Host $end_time
     do {
    Start-Sleep 1
    }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT @DTU_Storage AS "Pool Size DTU dominated by storage"
    ELSE 
    SELECT @DTU_Perf_99 AS "Pool Size DTU dominated by resource consumption";'
    
    #write-host $sql
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    
    

## Resumo

Nem todos os bancos de dados individuais são candidatos ideais para pools de banco de dados elástico. Bancos de dados com padrões de uso caracterizados por baixa utilização média e picos de utilização relativamente pouco frequentes são excelentes candidatos para pools elásticos. Padrões de uso do aplicativo são dinâmicos, por isso use as informações e ferramentas descritas neste artigo e informações para fazer uma avaliação inicial para verificar se um pool de banco de dados elástico é uma boa opção para alguns ou todos os bancos de dados. Este artigo é apenas um ponto de partida para ajudar a sua decisão de se um pool de banco de dados elástico é uma boa opção. Lembre-se de que você deve monitorar continuamente o uso do recurso histórico de recursos (usando STA ou DMVs) e reavaliar constantemente os níveis de desempenho de todos os bancos de dados. Tenha em mente que você pode adicionar e remover facilmente os bancos de dados dos pool elásticos e, se tiver um grande número de bancos de dados, poderá dividir os bancos de dados em vários pools de tamanhos variados.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=August15_HO6-->