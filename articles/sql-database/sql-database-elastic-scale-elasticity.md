<properties 
	pageTitle="Elasticidade de fragmento" 
	description="Explica conceitos e fornece exemplos por trás da elasticidade de fragmento, a capacidade de escalar horizontalmente facilmente bancos de dados do SQL Azure." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/17/2015" 
	ms.author="sidneyh"/>

# Elasticidade de fragmentos 

A **elasticidade de fragmento** permite aos desenvolvedores de aplicativos ampliar ou reduzir dinamicamente os recursos de banco de dados conforme a necessidade, habilitando a otimização do desempenho de seus aplicativos e também minimizando os custos. A combinação das **ferramentas de banco de dados elástico** para o Banco de Dados SQL do Azure juntamente com as [camadas de serviço Básica, Standard e Premium](http://msdn.microsoft.com/library/azure/dn741340.aspx) fornecem cenários de elasticidade muito atrativos. Ferramentas de banco de dados elástico habilitam o dimensionamento horizontal, um padrão de design no qual os bancos de dados (também [chamados de "fragmentos"](sql-database-elastic-scale-glossary.md)) são adicionados ou removidos de um conjunto de fragmentos para aumentar ou reduzir a capacidade. Da mesma forma, as camadas de serviço do Banco de Dados SQL fornecem recursos de **dimensionamento vertical** que recursos de um banco de dados individual podem escalar e reduzir verticalmente até que atendam à demanda de forma adequada. Juntos, a escala vertical de um único fragmento e a escala horizontal de vários fragmentos proporcionam aos desenvolvedores de aplicativos um ambiente muito flexível que pode ser dimensionado para atender às necessidades de desempenho, capacidade e otimização de custo.

Com a recém-lançada funcionalidade de **pools de banco de dados elástico**, fica ainda mais simples de atingir o dimensionamento vertical. Os pools permitem que o consumo de recursos de um banco de dados individual aumente ou diminua *automaticamente* dentro de um orçamento compartilhado por todo o pool. Para aplicativos que prefiram não aproveitar os pools de banco de dados elástico, este artigo descreverá outras técnicas para implementar um mecanismo baseado em políticas para gerenciar o dimensionamento vertical, bem como alguns cenários comuns para automatizar operações deste.

## Exemplo de dimensionamento horizontal: pico de conjunto

Um cenário canônico para escala horizontal é um aplicativo que processa transações para tíquetes de conjunto. Em volume do cliente normal, o aplicativo usa recursos de banco de dados mínimo para lidar com transações de compra. No entanto, quando tíquetes de venda para um concerto popular, um único banco de dados não pode manipular o grande pico nas demandas de clientes.

Para processar o aumento significativo nas transações, o aplicativo é dimensionado horizontalmente. O aplicativo agora pode distribuir a carga de transações entre vários fragmentos. Quando os recursos adicionais não são mais necessários, a camada de banco de dados é reduzido para o uso normal. Aqui, a escala horizontal permite que um aplicativo seja escalado horizontalmente para coincidir com a demanda dos clientes e escala verticalmente quando os recursos não são mais necessários.

## Exemplo de dimensionamento vertical: telemetria

Um cenário canônico para o dimensionamento vertical é um aplicativo que usa um **conjunto de fragmentos** para armazenar telemetria operacional. Nesse cenário, é melhor colocalizar todos os dados de telemetria para um único dia em um único fragmento. Neste aplicativo, os dados para o dia atual é incluídos em um fragmento e um novo fragmento é fornecido para os dias seguintes. Os dados operacionais podem ser antigos e consultados conforme apropriados.

Para incluir dados de telemetria em altas cargas, é melhor empregar um nível mais alto de serviço. Em outras palavras, um banco de dados Premium é melhor do que um banco de dados básico. Quando o banco de dados atinge sua capacidade, alterna entre inclusão para análise e relatório. Para fazer isso, desempenho da camada padrão é igual à tarefa. Assim, um poderá reduzir verticalmente a camada de serviço em fragmentos (que não seja o recém-criado) para atender às necessidades de desempenho inferiores para dados mais antigos.

A escala vertical também pode ser usada para aumentar o desempenho de um único banco de dados a fim de que ele atinja um desempenho aumentado. Um exemplo é um aplicativo de arquivamento de imposto. Em vez de arquivamento, é melhor manter os dados de um único cliente tudo no mesmo banco de dados e aumentar o desempenho desse fragmento. Dependendo do aplicativo, é vantajoso para escalar e reduzir verticalmente recursos, a fim de otimizar tanto os requisitos de custo quantos de desempenho.

Juntos, os dimensionamentos vertical e horizontal da camada de banco de dados formam o núcleo de aplicativos de escalabilidade e elasticidade de fragmentos.

Este documento fornece o contexto para cenários de elasticidade de fragmento, bem como referências às implementações de exemplo da escala vertical e da horizontal.

## Mecânica de elasticidade de fragmento 

A escala vertical e horizontal é uma função de três componentes básicos:

1. **Telemetria**
2. **Regra**
3. **Ação**   

## Telemetria

A **elasticidade orientada a dados** é a base de um aplicativo de escala elástica. Dependendo dos requisitos de desempenho, use telemetria para tomar decisões orientado a dados se dimensionar vertical ou horizontalmente.

#### Fontes de dados de telemetria
No contexto do Banco de Dados SQL do Azure, há inúmeras fontes principais que podem ser usados como fontes de dados para ter elasticidade de fragmento.

1. A **telemetria de desempenho** é apresentada dentro de cinco minutos na exibição **sys.resource_stats** 
2. A **telemetria de capacidade do banco de dados** por hora é apresentada por meio da exibição **sys.resource_usage**.  

Uma possível analisar o uso de recursos de desempenho consultando o mestre de banco de dados usando a consulta a seguir onde 'Shard_20140623' é o nome do banco de dados de destino.

    SELECT TOP 10 *  
    FROM sys.resource_stats  
    WHERE database_name = 'Shard_20140623'  
    ORDER BY start_time DESC 

A **telemetria de desempenho** pode ser resumida em um período de tempo (sete dias na consulta abaixo) para remover os comportamentos transitórios.

    SELECT  
    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent', 
        max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent', 
        avg(avg_physical_data_read_percent) AS 'Average Physical Data Read Utilization In Percent', 
        max(avg_physical_data_read_percent) AS 'Maximum Physical Data Read Utilization In Percent', 
        avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent', 
        max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent', 
        avg(active_session_count) AS 'Average # of Sessions', 
        max(active_session_count) AS 'Maximum # of Sessions', 
        avg(active_worker_count) AS 'Average # of Workers', 
        max(active_worker_count) AS 'Maximum # of Workers' 
    FROM sys.resource_stats  
    WHERE database_name = ' Shard_20140623' AND start_time > DATEADD(day, -7, GETDATE()); 

A **capacidade de banco de dados** pode ser medida com uma consulta semelhante em relação à exibição de **sys.resource_usage**. O máximo da coluna **storage_in_megabytes** produz o tamanho atual do banco de dados. Tal telemetria é útil para escala horizontal de um aplicativo quando um fragmento específico atinge sua capacidade.

    SELECT TOP 10 * 
    FROM [sys].[resource_usage] 
    WHERE database_name = 'Shard_20140623'  
    ORDER BY time DESC 

Como dados é incluídos em um fragmento específico, é útil um dia para frente do projeto e determinar se o fragmento tem capacidade suficiente para tratar a carga de trabalho próximo. Enquanto não é uma implementação verdadeira da regressão linear, a consulta a seguir retorna o delta máximo na capacidade do banco de dados entre dois dias consecutivos. Tal telemetria pode então ser aplicada a uma regra que resultaria em uma ação (ou em uma não ação) que está sendo executada.

    WITH MaxDatabaseDailySize AS( 
        SELECT 
            ROW_NUMBER() OVER (ORDER BY convert(date, [time]) DESC) as [Order], 
            CONVERT(date,[time]) as [date],  
            MAX(storage_in_megabytes) as [MaxSizeDay] 
        FROM [sys].[resource_usage] 
        WHERE  
            database_name = 'Shard_20140623' 
        GROUP BY CONVERT(date,[time]) 
        ) 
    
    SELECT 
        MAX(ISNULL(Size.[MaxSizeDay] - PreviousDaySize.[MaxSizeDay], 0)) 
    FROM  
        MaxDatabaseDailySize Size INNER JOIN 
        MaxDatabaseDailySize PreviousDaySize ON Size.[order]+1 = PreviousDaySize.[order] 
    WHERE 
        Size.[order] < 8 

## Regra  

A regra é o mecanismo de decisão que determina se ou não uma ação é executada. Algumas regras são muito simples e alguns são muito mais complicados. Conforme mostrado no trecho de código abaixo, uma regra centrados na capacidade pode ser configurada de forma que quando um fragmento atinge $SafetyMargin, por exemplo, 80% da sua capacidade máxima, um novo fragmento é provisionado.

    # Determine if the current DB size plus the maximum daily delta size is greater than the threshold 
    if( ($CurrentDbSizeMb + $MaxDbDeltaMb) -gt ($MaxDbSizeMb * $SafetyMargin))  
    {#provision new shard} 

Considerando as fontes de dados acima, um número de regras pode ser formulado para realizar diversas situações de elasticidade de fragmento.

## Ação  

Com base no resultado da regra, a ação (ou a não ação) é o resultado. As duas ações mais comuns são:

* O aumento ou diminuição do nível ou o desempenho do nível de serviço do fragmento 
* A adição ou remoção de um fragmento de um conjunto de fragmentos.

Observe que em soluções de escala horizontais e verticais, o resultado de uma ação não for imediato. Ao escalar verticalmente, por exemplo, a emissão do comando ALTER DATABASE para aumentar o nível de serviço de um banco de dados básico para um banco de dados Premium leva uma quantidade variável de tempo. A duração depende do tamanho do banco de dados (para obter mais informações, consulte [Alterar níveis de serviço e níveis de desempenho](http://msdn.microsoft.com/library/azure/dn369872.aspx). Da mesma forma, a alocação ou o provisionamento de um novo fragmento não é instantâneo. Assim, para escala vertical e horizontal, aplicativos devem levar em conta o tempo diferente de zero para alterar ou provisionar um novo banco de dados.

## Cenário de elasticidade de fragmento de exemplo 

O exemplo mostrado na figura a seguir destaca os dois cenários de dimensionamento elástico: 1. O dimensionamento horizontal de um mapa de fragmentos 2. O dimensionamento vertical de um fragmento individual.

![Inclusão de dados Operationl][1]

A escala horizontal, uma regra (com base no tamanho do banco de dados ou data) é usada para provisionar um novo fragmento e registrá-lo com o mapa do fragmento, aumentando assim a camada de banco de dados horizontalmente. Em segundo lugar, para escalar verticalmente, uma segunda regra é implementada em que qualquer fragmento é mais de um dia está desatualizado da Premium Edition para uma edição básica ou padrão.

Considere o cenário de telemetria novamente: os fragmentos de aplicativo por data. Ele coleta dados por telemetria continuamente, exigindo uma edição de alto desempenho no carregamento de tempo, mas idades de desempenho mais baixo do que os dados. A data do dia atual [Tnow] é gravada para um banco de dados de alto desempenho (Premium). Quando o relógio bater meia-noite, o fragmento do dia anterior (agora T-1) não será mais usado para inclusão. Os dados atuais são incluídos pelo Tnow atual. Antes do próximo dia, um novo fragmento deve ser provisionado e registrado com o mapa do fragmento (T+1).

Isso pode ser feito por um provisionamento de um novo fragmento antes de cada novo dia ou provisionar um novo fragmento quando o fragmento atual (Tnow) se aproximar de sua capacidade máxima. Usar um desses métodos preserva a localidade dos dados de telemetria todos escrita para um dia específico. Também pode ser aplicada a fragmentação por hora granularidade mais fina. Depois que um novo fragmento é provisionado e como T-1 é usado para consultas e relatórios, é desejável reduzir o nível de desempenho do banco de dados para reduzir os custos. Como o conteúdo na duração de bancos de dados, o nível de desempenho pode ser reduzido e/ou o conteúdo de bancos de dados pode ser arquivado no armazenamento do Azure ou excluído, dependendo do aplicativo.

## Cenários de elasticidade de fragmento em execução  

Para facilitar a implementação real de cenários de dimensionamento vertical e horizontal, vários [scripts de exemplo de elasticidade de fragmento](http://go.microsoft.com/?linkid=9862617) foram criados e lançados no Script Center. Esses runbooks PowerShell criados para serem executados no serviço de automação do Azure fornecem uma série de métodos que interagem com as bibliotecas de cliente de escala elástica e Banco de Dados SQL do Azure. Por fazer a compilação ou a extração desses exemplos de código, uma pessoa pode criar os scripts necessários a fim de automatizar os cenários de escala vertical, horizontal, ou ambos, para seu aplicativo.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-elasticity/data-ingestion.png

<!--anchors-->
[Telemetry]: #telemetry
[Rule]: #rule
[Action]: #action
 

<!---HONumber=July15_HO4-->