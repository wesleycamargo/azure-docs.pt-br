<properties 
	pageTitle="Enviar consultas de Hive para clusters do Hadoop no processo de análise avançada | Microsoft Azure" 
	description="Processar dados de tabelas Hive" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="hangzh;bradsev" />

#<a name="heading"></a> Enviar consultas de Hive para clusters do Hadoop do HDInsight no processo de análise avançada

Este documento descreve várias maneiras de enviar consultas de Hive para clusters de Hadoop gerenciados por um serviço do HDInsight no Azure. Consultas de Hive podem ser enviadas usando:

* a Linha de Comando do Hadoop no nó principal do cluster
* o IPython Notebook 
* o Editor de Hive
* Scripts do PowerShell do Azure 

Consultas de Hive genéricas que podem usadas para explorar os dados ou gerar recursos que usam UDFs (Funções Definidas pelo Usuário) Hive são fornecidas.

Exemplos de consultas específicas para cenários de [Dados de Viagens de Táxi em NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) também são fornecidos no [repositório Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Essas consultas já têm o esquema de dados especificado e estão prontas para serem enviadas para execução nesse cenário.

Na seção final são discutidos os parâmetros que os usuários podem ajustar para que o desempenho das consultas do Hive possa ser melhorado.

## Pré-requisitos
Este artigo supõe que você:
 
* criou uma conta de armazenamento do Azure. Se precisar de instruções para esta tarefa, confira [Criar uma conta de Armazenamento do Azure](../hdinsight-get-started.md#storage) 
* provisionou um cluster do Hadoop com o serviço HDInsight. Se precisar de instruções, consulte [Provisionar um cluster HDInsight](../hdinsight-get-started.md#provision).
* Os dados foram carregados para tabelas Hive em clusters do Hadoop do Azure HDInsight. Se não tiverem sido, siga as instruções fornecidas em [Criar e carregar dados para tabelas Hive](machine-learning-data-science-hive-tables.md) para carregar dados para tabelas Hive primeiro.
* habilitou o acesso remoto ao cluster. Se precisar de instruções, consulte [Acessar o nó principal do Cluster do Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess). 


## <a name="submit"></a>Como enviar consultas de Hive

1. [Enviar consultas de Hive por meio de Linha de comando do Hadoop no nó principal do cluster Hadoop](#headnode)
2. [Enviar consultas de Hive com o Editor de Hive](#hive-editor)
3. [Enviar consultas de Hive com comandos do PowerShell do Azure](#ps)
 
###<a name="headnode"></a> 1. Enviar consultas de Hive por meio de Linha de comando do Hadoop no nó principal do cluster Hadoop

Se a consulta é complexa, o envio de consultas de Hive diretamente ao nó principal do cluster Hadoop normalmente leva a um resultado mais rápido do que enviá-la com scripts do Editor de Hive ou do PowerShell do Azure.

Faça logon no nó principal do cluster do Hadoop, abra a Linha de Comando do Hadoop na área de trabalho do nó principal e digite o comando `cd %hive_home%\bin`.

Os usuários têm três maneiras de enviar consultas de Hive na Linha de Comando do Hadoop:

* diretamente
* usando arquivos .hql
* com o console de comando do Hive

#### Enviar consultas de Hive diretamente na Linha de Comando do Hadoop. 

Os usuários podem executar o comando como `hive -e "<your hive query>;` para enviar consultas de Hive simples diretamente na Linha de Comando do Hadoop. Veja um exemplo, no qual a caixa vermelha descreve o comando que envia a consulta Hive e a caixa verde descreve a saída da consulta Hive.

![Criar espaço de trabalho][10]

#### Enviar consultas de Hive em arquivos de .hql

Quando a consulta Hive é mais complicada e tem várias linhas, não é prático editar consultas na linha de comando ou no console de comando de Hive. Uma alternativa é usar um editor de texto no nó principal do cluster do Hadoop para salvar as consultas de Hive em um arquivo .hql em um diretório local do nó principal. Em seguida, a consulta de Hive no arquivo HQL pode ser enviada usando o argumento `-f` da seguinte maneira:
	
	`hive -f "<path to the .hql file>"`

![Criar espaço de trabalho][15]


**Suprimir a impressão da tela de status de progresso de consultas de Hive**

Por padrão, após a consulta Hive ser enviada na Linha de Comando do Hadoop, o andamento do trabalho de Mapear/Reduzir será ser impresso na tela. Para suprimir a impressão da tela de andamento do trabalho de Mapear/Reduzir, você pode usar um argumento `-S` ("S" em letra maiúscula) na linha de comando da seguinte maneira:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Enviar consultas de Hive no console de comando de Hive.

Os usuários também podem entrar primeiro no console de comando de Hive executando o comando `hive` na Linha de Comando do Hadoop e enviar consultas de Hive no console de comando de Hive. Aqui está um exemplo. Neste exemplo, as duas caixas vermelhas realçam os comandos usados para inserir o console de comando de Hive e a consulta de Hive enviada no console de comando de Hive, respectivamente. A caixa verde realça a saída da consulta de Hive.

![Criar espaço de trabalho][11]

Os exemplos anteriores mostram os resultados da consulta de Hive diretamente na tela. Os usuários também podem gravar a saída em um arquivo local no nó principal ou em um blob do Azure. Em seguida, os usuários podem usar outras ferramentas para analisar com mais detalhes a saída das consultas de Hive.

**Resultados da consulta de Hive em um arquivo local de saída.**

Para exibir os resultados da consulta de Hive em um diretório local no nó principal, os usuários precisam enviar a consulta de Hive na Linha de Comando do Hadoop da seguinte maneira:

	`hive -e "<hive query>" > <local path in the head node>`

No exemplo a seguir, a saída da consulta de Hive é gravada em um arquivo `hivequeryoutput.txt` no diretório `C:\apps\temp`.

![Criar espaço de trabalho][12]

**Exportar a saída de consulta de Hive para um blob do Azure**

Os usuários também podem exportar a saída da consulta de Hive para um blob do Azure dentro do contêiner padrão do cluster do Hadoop. A consulta de Hive deve ficar assim:

	`insert overwrite directory wasb:///<directory within the default container> <select clause from ...>`

No exemplo a seguir, a saída da consulta de Hive é gravada em um diretório de blob `queryoutputdir` no contêiner padrão do cluster do Hadoop. Aqui, você precisa fornecer apenas o nome do diretório, sem o nome do blob. Um erro será gerado se você fornecer os nomes do blob e do diretório, como `wasb:///queryoutputdir/queryoutput.txt`.

![Criar espaço de trabalho][13]

Se você abrir o contêiner padrão do cluster do Hadoop usando ferramentas como o Gerenciador de Armazenamento do Azure, você verá a saída da consulta de Hive da seguinte maneira. Você pode aplicar o filtro (destacado pela caixa vermelha) para recuperar apenas o blob com letras específicas nos nomes.

![Criar espaço de trabalho][14]

###<a name="hive-editor"></a> 2. Enviar consultas de Hive com o Editor de Hive

Os usuários também podem usar o Console de Consultas (Editor de Hive) digitando a URL em um navegador da Web `https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor` (as credenciais do cluster do Hadoop serão solicitadas para fazer logon),

###<a name="ps"></a> 3. Enviar consultas de Hive com comandos do PowerShell do Azure

Os usuários também podem usar o PowerShell para enviar consultas de Hive. Para obter instruções, confira [Enviar trabalhos do Hive usando o PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).

## <a name="explore"></a>Exploração de dados, engenharia de recurso e ajuste de parâmetros de Hive

Descrevemos as seguintes tarefas de disputa de dados nesta seção usando o Hive em clusters Hadoop do HDInsight do Azure:

1. [Exploração de dados](#hive-dataexploration)
2. [Geração de recursos](#hive-featureengineering)

> [AZURE.NOTE]As consultas de Hive de exemplo pressupõem que os dados foram carregados para tabelas Hive em clusters do Hadoop do Azure HDInsight. Se não, siga as instruções de [Criar e carregar dados nas tabelas Hive](machine-learning-data-science-hive-tables.md) para carregar dados nas tabelas Hive primeiro.

###<a name="hive-dataexploration"></a>Exploração de dados
Aqui estão alguns scripts de Hive de exemplo que podem ser usados para explorar dados em tabelas Hive.

1. Obter a contagem de observações por partição `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obter a contagem de observações por dia `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obter os níveis em uma coluna categórica `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obter o número de níveis na combinação de duas colunas categóricas `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obter a distribuição de colunas numéricas `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extrair registros de associação de duas tabelas

	    SELECT 
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>, 
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>, 
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

###<a name="hive-featureengineering"></a>Geração de recursos

Nesta seção, descrevemos maneiras de gerar recursos usando consultas de Hive:
  
1. [Geração de recursos baseada em frequência](#hive-frequencyfeature)
2. [Riscos de variáveis categóricas na classificação binária](#hive-riskfeature)
3. [Extrair recursos do campo Datetime](#hive-datefeature)
4. [Extrair recursos de campo de texto](#hive-textfeature)
5. [Calcular a distância entre coordenadas de GPS](#hive-gpsdistance)

> [AZURE.NOTE]Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser unida com a tabela original.

####<a name="hive-frequencyfeature"></a> Geração de recursos baseada na frequência

Às vezes, é importante calcular as frequências dos níveis de uma variável categórica ou as frequências de combinações de nível de diversas variáveis categóricas. Os usuários podem usar o script a seguir para calcular as frequências:

		select 
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select 
				<column_name1>,<column_name2>, count(*) as sub_count 
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;
	

####<a name="hive-riskfeature"></a> Riscos de variáveis categóricas na classificação binária

Na classificação binária, por vezes é necessário converter variáveis categóricas não numéricas em recursos numéricos substituindo os níveis não numéricos por riscos numéricos, já que alguns modelos aceitam apenas recursos numéricos. Nesta seção, mostraremos algumas consultas de Hive genéricas para calcular os valores de risco (probabilidade de log) de uma variável categórica.


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select 
	    	<column_name1>,<column_name2>, 
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select 
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select 
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename> 
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b 

Neste exemplo, as variáveis `smooth_param1` e `smooth_param2` são definidas para suavizar os valores de risco calculados por meio dos dados. Os riscos variam entre -Inf e Inf. Riscos>0 significa a probabilidade de que o destino igual a 1 seja maior que 0,5.

Depois da tabela de risco ser calculada, os usuários podem atribuir valores de risco a uma tabela associando-a à tabela de risco. A consulta de junção de Hive foi indicada na seção anterior.

####<a name="hive-datefeature"></a> Extrair recursos de campos de data e hora

O Hive vem com um conjunto de UDFs para processar campos datetime. No Hive, o formato de datetime padrão é “aaaa-MM-dd 00:00:00” (como “1970-01-01 12:21:32”). Nesta seção, vamos mostrar exemplos de como extrair o dia do mês e o mês de um campo datetime e exemplos de conversão de uma cadeia de caracteres de datetime em um formato diferente do formato padrão para uma cadeia de caracteres de datetime no formato padrão.

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

Essa consulta de Hive pressupõe que o `<datetime field>` esteja no formato datetime padrão.

Se um campo datetime não estiver no formato padrão, é necessário primeiro converter o campo datetile em carimbo de data/hora de Unix e, em seguida, converter o carimbo de data/hora do Unix em uma cadeia de caracteres datetime no formato padrão. Depois que o datetime estiver no formato padrão, os usuários poderão aplicar UDFs datetime incorporadas para extrair recursos.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

Nesta consulta, se `<datetime field>` tem o padrão `03/26/2015 12:04:39`, o `'<pattern of the datetime field>'` deve ser `'MM/dd/yyyy HH:mm:ss'`. Para testá-lo, os usuários podem executar

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

Nesta consulta, `hivesampletable` vem com todos os clusters do Hadoop do Azure HDInsight por padrão quando os clusters são provisionados.


####<a name="hive-textfeature"></a> Extrair recursos de campos de texto

Suponha que a tabela Hive tem um campo de texto, que é uma cadeia de caracteres de palavras separadas por espaços, a consulta a seguir extrai o comprimento da cadeia de caracteres e o número de palavras na cadeia de caracteres.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

####<a name="hive-gpsdistance"></a> Calcular a distância entre coordenadas de GPS

A consulta fornecida nesta seção pode ser aplicada diretamente aos Dados de Viagens de Táxi em NYC. A finalidade dessa consulta é mostrar como aplicar as funções matemáticas incorporadas no Hive para gerar recursos.

Os campos que são usados nesta consulta são coordenadas de GPS de locais de saída e chegada, chamadas pickup_longitude, pickup_latitude, dropoff_longitude e dropoff_latitude. As consultas para calcular a distância direta entre as coordenadas de saída e chegada são:

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, 
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
		from nyctaxi.trip 
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10; 

As equações matemáticas do cálculo da distância entre duas coordenadas de GPS podem ser encontradas [aqui](http://www.movable-type.co.uk/scripts/latlong.html), as quais foram criadas por Peter Lapisu. No Javascript dele, a função toRad() é apenas `lat_or_lon*pi/180`, que converte graus em radianos. Aqui, `lat_or_lon` é a latitude ou a longitude. Como o Hive não fornece a função `atan2`, mas fornece a função `atan`, a função `atan2` é implementada pela função `atan` na consulta de Hive acima, com base na definição fornecida na [Wikipédia](http://en.wikipedia.org/wiki/Atan2).

![Criar espaço de trabalho][1]

Uma lista completa de UDFs de Hive inseridas pode ser encontrada [aqui](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions).

## <a name="tuning"></a> Tópicos avançados: ajustar parâmetros de Hive para melhorar a velocidade de consulta

As configurações de parâmetro padrão do cluster de Hive talvez não sejam adequadas para as consultas de Hive e os dados que as consultas estão processando. Nesta seção, discutiremos alguns parâmetros que os usuários podem ajustar para que o desempenho das consultas de Hive possa ser melhorado. Os usuários precisam adicionar as consultas de ajuste de parâmetro antes das consultas de processamento de dados.

1. Espaço de heap de Java: para consultas que envolvem ingressar grandes conjuntos de dados ou processar longos registros, um erro comum é **ficar sem espaço de heap**. Isso pode ser ajustado ao definir os parâmetros `mapreduce.map.java.opts` e `mapreduce.task.io.sort.mb` com os valores desejados. Veja um exemplo:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	Esse parâmetro aloca memória de 4 GB para o espaço de heap de Java e também torna a classificação mais eficiente ao alocar mais memória para ela. É uma boa ideia explorá-lo se houver erros de falha de trabalho relacionados ao espaço de heap.

2. Tamanho do bloco de DFS: esse parâmetro define a menor unidade de dados armazenada pelo sistema de arquivos. Por exemplo, se o tamanho do bloco de DFS for 128 MB, todos os dados de tamanho menor ou até 128 MB serão armazenados em um único bloco, enquanto os dados maiores que 128 MB serão alocados em blocos extras. Escolher um tamanho de bloco muito pequeno causa grandes sobrecargas no Hadoop, pois o nó de nome precisa processar muitas solicitações a mais para localizar o bloco relevante em relação ao arquivo. Uma configuração recomendada ao trabalhar com gigabytes de dados (ou mais ainda) é:

		set dfs.block.size=128m;

3. Otimizar a operação de ingresso no Hive: embora operações de ingresso na estrutura de mapear/reduzir geralmente ocorram na fase de redução, algumas vezes, é possível obter enormes ganhos agendando associações na fase de mapeamento (também chamada de "mapjoins"). Para direcionar o Hive para fazer isso sempre que possível, podemos definir:

		set hive.auto.convert.join=true;

4. Especificar o número de mapeadores de Hive: embora o Hadoop permita ao usuário definir o número de redutores, o número de mapeadores pode não ser normalmente definido pelo usuário. Um truque que permite certo grau de controle sobre esse número é escolher as variáveis do hadoop, mapred.min.split.size e mapred.max.split.size. O tamanho de cada tarefa de mapeamento é determinado por:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	Normalmente, o valor padrão de mapred.min.split.size é 0, o de mapred.max.split.size é Long.MAX e o de dfs.block.size é 64 MB. Como vemos, dado o tamanho dos dados, ajustar esses parâmetros ao “configurá-los” permite ajustar o número de mapeadores usado.

5. Algumas outras opções mais avançadas para otimizar o desempenho de Hive são mencionadas a seguir. Eles permitem configurar a memória alocada para mapear e reduzir as tarefas e podem ser úteis no ajuste de desempenho. Tenha em mente que o `mapreduce.reduce.memory.mb` não pode ser maior que o tamanho da memória física de cada nó de trabalho no cluster do Hadoop.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-hive-queries/atan2new.png
[10]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-hive-queries/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-hive-queries/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-hive-queries/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-3.png


 

<!---HONumber=July15_HO4-->