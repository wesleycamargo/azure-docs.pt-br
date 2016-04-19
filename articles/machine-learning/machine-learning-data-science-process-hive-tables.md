<properties
	pageTitle="Enviar consultas de Hive para clusters do Hadoop no processo e tecnologia de análise avançada | Microsoft Azure"
	description="Processar dados de tabelas Hive com consultas de Hive."
	services="machine-learning"
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
	ms.date="02/08/2016"
	ms.author="hangzh;bradsev" />

#<a name="heading"></a> Enviar consultas de Hive para clusters do Hadoop do HDInsight no processo e tecnologia de análise avançada

Este documento descreve várias maneiras de enviar consultas de Hive para clusters de Hadoop gerenciados por um serviço do HDInsight no Azure. Esta tarefa é uma etapa no CAP (Processo de Análise da Cortana). Várias tarefas de wrangling de dados são discutidas: exploração de dados e geração de recursos. Consultas genéricas de Hive que mostram como explorar dados ou gerar recursos usando o Hive em um cluster do Hadoop do Azure HDInsight são apresentadas. Essas consultas de Hive usam UDFs (Funções Definidas pelo Usuário) de Hive incorporadas que são fornecidas.

Exemplos de consultas que são específicas para cenários de [Dados de Viagens de Táxi em NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) também são fornecidos no [repositório Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Essas consultas já tem o esquema de dados especificado e estão prontas para ser enviadas para execução.

Na seção final são discutidos os parâmetros que podem ser usados para ajustar e aprimorar o desempenho das consultas do Hive.

## Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento do Azure](../hdinsight-get-started.md#storage)
* Provisionou um cluster do Hadoop personalizado com o serviço HDInsight. Se precisar de instruções, consulte [Personalizar os clusters do Hadoop do Azure HDInsight para análise avançada](machine-learning-data-science-customize-hadoop-cluster.md).
* Os dados foram carregados para tabelas Hive em clusters do Hadoop do Azure HDInsight. Se não, siga as instruções de [Criar e carregar dados nas tabelas Hive](machine-learning-data-science-move-hive-tables.md) para carregar dados nas tabelas Hive primeiro.
* Habilitou o acesso remoto ao cluster. Se precisar de instruções, consulte [Acessar o nó principal do Cluster do Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


## <a name="submit"></a>Como enviar consultas de Hive
Consultas de hive podem ser enviadas usando os seguintes aplicativos:

* **Console de Linha de Comando do Hadoop** no nó principal do cluster
* **IPython Notebook**
* **Editor de Hive**
* Scripts do **PowerShell**

Consultas de Hive são semelhantes ao SQL. Usuários familiarizados com o SQL podem achar o <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">Roteiro de SQL para Hive</a> útil.

Ao enviar uma consulta de Hive, você também pode controlar o destino da saída de consultas de Hive, seja na tela, para um arquivo local no nó principal ou para um blob do Azure.

### Por meio do Console de Linha de Comando do Hadoop no nó principal do Cluster do Hadoop

Se a consulta for complexa, enviar de consultas de Hive diretamente do nó principal do cluster do Hadoop normalmente gera resultados mais rápidos do que enviá-las com um Editor de Hive ou usando scripts do PowerShell do Azure.

Faça logon no nó principal do cluster do Hadoop, abra a Linha de Comando do Hadoop na área de trabalho do nó principal e digite o comando

    cd %hive_home%\bin

Os usuários têm três maneiras de enviar consultas de Hive no console de Linha de Comando do Hadoop:

* diretamente da linha de comando do Hadoop
* usando arquivos .hql
* pelo console de comando de Hive

#### Enviar consultas de Hive diretamente da Linha de Comando do Hadoop

Os usuários podem executar um comando como

	hive -e "<your hive query>;

para enviar consultas de Hive simples diretamente pela linha de comando do Hadoop. Veja um exemplo, no qual a caixa vermelha descreve o comando que envia a consulta Hive e a caixa verde descreve a saída da consulta Hive.

![Criar espaço de trabalho](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png)

#### Enviar consultas de Hive em arquivos de .hql

Quando a consulta de Hive é mais complicada e tem várias linhas, não é prático editar consultas na linha de comando ou no console de comando de Hive. Uma alternativa é usar um editor de texto no nó principal do cluster do Hadoop e salvar as consultas de Hive em um arquivo .hql em um diretório local do nó principal. Em seguida, a consulta de Hive no arquivo HQL pode ser enviada usando o argumento `-f` no comando `hive` da seguinte maneira:

	`hive -f "<path to the .hql file>"`


#### Suprimir a impressão da tela de status de progresso de consultas de Hive

Por padrão, após a consulta de Hive ser enviada no console de Linha de Comando do Hadoop, o andamento do trabalho de Mapear/Reduzir será ser impresso na tela. Para suprimir a impressão da tela de andamento do trabalho de Mapear/Reduzir, você pode usar o argumento `-S` (diferencia maiúsculas de minúsculas) na linha de comando da seguinte maneira:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Enviar consultas de Hive no console de comando de Hive.

Os usuários também podem entrar no console de comando de Hive executando o comando `hive` na Linha de Comando do Hadoop e enviar consultas de Hive no console de comando do Hive no prompt **hive>**. Aqui está um exemplo.

![Criar espaço de trabalho](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png)

Neste exemplo, as duas caixas vermelhas realçam os comandos usados para inserir o console de comando de Hive e a consulta de Hive enviada no console de comando de Hive, respectivamente. A caixa verde realça a saída da consulta de Hive.

Os exemplos anteriores mostram os resultados da consulta de Hive diretamente na tela. Os usuários também podem gravar a saída em um arquivo local no nó principal ou em um blob do Azure. Em seguida, os usuários podem usar outras ferramentas para analisar com mais detalhes a saída das consultas de Hive.

#### Resultados da consulta de Hive em um arquivo local de saída.

Para exibir os resultados da consulta de Hive em um diretório local no nó principal, os usuários precisam enviar a consulta de Hive na Linha de Comando do Hadoop da seguinte maneira:

	`hive -e "<hive query>" > <local path in the head node>`


#### Exportar a saída de consulta de Hive para um blob do Azure

Os usuários também podem exportar a saída da consulta de Hive para um blob do Azure dentro do contêiner padrão do cluster do Hadoop. A consulta de Hive para fazer isso é semelhante a esta:

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

No exemplo a seguir, a saída da consulta de Hive é gravada em um diretório de blob `queryoutputdir` no contêiner padrão do cluster do Hadoop. Aqui, você deve fornecer somente o nome do diretório, sem o nome do blob. Um erro será gerado se você fornecer os nomes do blob e do diretório, como **wasb:///queryoutputdir/queryoutput.txt*.

![Criar espaço de trabalho](./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png)

A saída da consulta de Hive pode ser vista no armazenamento de blob abrindo o contêiner padrão do cluster do Hadoop usando a ferramenta Gerenciador de Armazenamento do Azure (ou equivalente). Você pode aplicar o filtro (destacado pela caixa vermelha) se quiser recuperar um blob com letras especificadas em nomes.

![Criar espaço de trabalho](./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png)

### Por meio do Editor de Hive ou de comandos do PowerShell do Azure

Os usuários também podem usar o Console de Consulta (Editor de Hive) digitando a URL do formulário

*https://&#60;Hadoop nome do cluster>.azurehdinsight.net/Home/HiveEditor*

em um navegador da Web. Observe que você será solicitado a inserir as credenciais do cluster do Hadoop para fazer logon. Como alternativa, você pode [Enviar trabalhos Hive usando o PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


##<a name="hive-dataexploration"></a>Exploração de dados
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

##<a name="hive-featureengineering"></a>Geração de recursos

Nesta seção, descrevemos maneiras de gerar recursos usando consultas de Hive.

> [AZURE.NOTE] As consultas de Hive de exemplo nesta seção pressupõem que os dados foram carregados tabelas Hive em clusters de Hadoop do Azure HDInsight. Se não, siga as instruções de [Criar e carregar dados nas tabelas Hive](machine-learning-data-science-move-hive-tables.md) para carregar dados nas tabelas Hive primeiro.

Depois de gerar recursos adicionais, você pode adicioná-los como colunas à tabela existente ou criar uma nova tabela com os recursos adicionais e a chave primária, que pode ser associada à tabela original.

1. [Geração de recursos baseada em frequência](#hive-frequencyfeature)
2. [Riscos de variáveis categóricas na classificação binária](#hive-riskfeature)
3. [Extrair recursos do campo Datetime](#hive-datefeatures)
4. [Extrair recursos de campo de texto](#hive-textfeatures)
5. [Calcular a distância entre coordenadas de GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Geração de recursos baseada em frequência

Geralmente é útil calcular as frequências dos níveis de uma variável categórica ou as frequências de determinadas combinações de níveis de diversas variáveis categóricas. Os usuários podem usar o script a seguir para calcular tais frequências:

		select
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select
				<column_name1>,<column_name2>, count(*) as sub_count
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;


###<a name="hive-riskfeature"></a>Riscos de variáveis categóricas na classificação binária

Na classificação binária, é necessário converter variáveis categóricas não numéricas em recursos numéricos quando os modelos usados aceitam apenas recursos numéricos. Isso é feito substituindo cada nível não numéricos por um risco numérico. Nesta seção, mostraremos algumas consultas de Hive genéricas para calcular os valores de risco (probabilidade de log) de uma variável categórica.


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

Neste exemplo, as variáveis `smooth_param1` e `smooth_param2` são definidas para suavizar os valores de risco calculados por meio dos dados. Riscos tem um intervalo entre -Inf e Inf. Riscos > 0 indica que a probabilidade de o destino ser igual a 1 é maior que 0,5.

Depois da tabela de risco ser calculada, os usuários podem atribuir valores de risco a uma tabela associando-a à tabela de risco. A consulta de associação de Hive foi indicada na seção anterior.

###<a name="hive-datefeatures"></a>Extrair recursos de campos Datetime

O Hive vem com um conjunto de UDFs para processar campos datetime. No Hive, o formato de datetime padrão é “aaaa-MM-dd 00:00:00” (“1970-01-01 12:21:32”, por exemplo). Nesta seção, vamos mostrar exemplos de como extrair o dia do mês e o mês de um campo datetime, bem como exemplos de conversão de uma cadeia de caracteres de datetime em um formato diferente do formato padrão para uma cadeia de caracteres de datetime no formato padrão.

    	select day(<datetime field>), month(<datetime field>)
		from <databasename>.<tablename>;

Essa consulta de Hive pressupõe que o *&#60;campo datetime>* esteja no formato datetime padrão.

Se um campo datetime não estiver no formato padrão, é necessário primeiro converter o campo datetime em carimbo de data/hora de Unix e, em seguida, converter o carimbo de data/hora do Unix em uma cadeia de caracteres datetime no formato padrão. Quando o datetime estiver no formato padrão, os usuários poderão aplicar UDFs datetime incorporadas para extrair recursos.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

Nesta consulta, se o *&#60;campo datetime>* tem o padrão *03/26/2015 12:04:39*, o *'&#60;padrão do campo datetime >'* deve ser `'MM/dd/yyyy HH:mm:ss'`. Para testá-lo, os usuários podem executar

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

O *hivesampletable* nesta consulta vem pré-instalado com todos os clusters do Hadoop do Azure HDInsight por padrão quando os clusters são provisionados.


###<a name="hive-textfeatures"></a>Extrair recursos de campos de Texto

Quando a tabela Hive tem um campo de texto que contém uma cadeia de caracteres de palavras delimitada por espaços, a consulta a seguir extrai o comprimento da cadeia de caracteres e o número de palavras na cadeia de caracteres.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
		from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Calcular a distância entre conjuntos de coordenadas de GPS

A consulta fornecida nesta seção pode ser aplicada diretamente aos Dados de Viagens de Táxi em NYC. A finalidade dessa consulta é mostrar como aplicar funções matemáticas incorporadas no Hive para gerar recursos.

Os campos que são usados nesta consulta são coordenadas de GPS de locais de saída e chegada, chamadas *pickup\_longitude*, *pickup\_latitude*, *dropoff\_longitude* e *dropoff\_latitude*. As consultas que calculam a distância direta entre as coordenadas de saída e chegada são:

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

As equações matemáticas que calculam a distância entre duas coordenadas de GPS podem ser encontradas no site <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Scripts de Tipo Móvel</a>, as quais foram criadas por Peter Lapisu. No Javascript dele, a função `toRad()` é apenas *lat\_or\_lon*pi/180*, que converte graus em radianos. Aqui, *lat\_or\_lon* é a latitude ou a longitude. Como o Hive não fornece a função `atan2`, mas fornece a função `atan`, a função `atan2` é implementada pela função `atan` na consulta de Hive acima usando a definição fornecida na <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipédia</a>.

![test](./media/machine-learning-data-science-process-hive-tables/atan2new.png)

Uma lista completa de UDFs de Hive inseridas pode ser encontrada na seção **Funções Internas** no <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wiki do Apache Hive</a>.

## <a name="tuning"></a> Tópicos avançados: ajustar parâmetros de Hive para melhorar a velocidade de consulta

As configurações de parâmetro padrão do cluster de Hive talvez não sejam adequadas para as consultas de Hive e os dados que as consultas estão processando. Nesta seção, discutiremos alguns parâmetros que os usuários podem ajustar para melhorar o desempenho das consultas de Hive. Os usuários precisam adicionar as consultas de ajuste de parâmetro antes das consultas de processamento de dados.

1. **Espaço de heap de Java**: para consultas que envolvem ingressar grandes conjuntos de dados ou processar longos registros, um erro comum é **ficar sem espaço de heap**. Isso pode ser ajustado definindo os parâmetros *mapreduce.map.java.opts* e *mapreduce.task.io.sort.mb* com os valores desejados. Aqui está um exemplo:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;


	Esse parâmetro aloca memória de 4 GB para o espaço de heap de Java e também torna a classificação mais eficiente ao alocar mais memória para ela. É uma boa ideia explorar essas alocações se houver erros de falha de trabalho relacionados ao espaço de heap.

2. **Tamanho do bloco de DFS**: esse parâmetro define a menor unidade de dados armazenada pelo sistema de arquivos. Por exemplo, se o tamanho do bloco de DFS for 128 MB, todos os dados de tamanho menor ou até 128 MB serão armazenados em um único bloco, enquanto os dados maiores que 128 MB serão alocados em blocos extras. Escolher um tamanho de bloco muito pequeno causa grandes sobrecargas no Hadoop, pois o nó de nome precisa processar muitas solicitações a mais para localizar o bloco relevante em relação ao arquivo. Uma configuração recomendada ao trabalhar com gigabytes de dados (ou mais ainda) é:

		set dfs.block.size=128m;

3. **Otimizar a operação de ingresso no Hive**: embora operações de ingresso na estrutura de mapear/reduzir geralmente ocorram na fase de redução, algumas vezes, é possível obter enormes ganhos agendando associações na fase de mapeamento (também chamada de "mapjoins"). Para direcionar o Hive para fazer isso sempre que possível, podemos definir:

		set hive.auto.convert.join=true;

4. **Especificar o número de mapeadores de Hive**: embora o Hadoop permita ao usuário definir o número de redutores, o número de mapeadores não é normalmente definido pelo usuário. Um truque que permite certo grau de controle sobre esse número é escolher as variáveis do Hadoop, *mapred.min.split.size* e *mapred.max.split.size*, pois cada tarefa de mapeamento é determinada por:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	Normalmente, o valor padrão de *mapred.min.split.size* é 0, o de *mapred.max.split.size* é **Long.MAX** e o de *dfs.block.size* é 64 MB. Como vemos, dado o tamanho dos dados, ajustar esses parâmetros ao “configurá-los” permite ajustar o número de mapeadores usado.

5. Algumas outras opções mais **avançadas** para otimizar o desempenho de Hive são mencionadas a seguir. Eles permitem definir a memória alocada para mapear e reduzir as tarefas e podem ser úteis no ajuste de desempenho. Tenha em mente que o *mapreduce.reduce.memory.mb* não pode ser maior que o tamanho da memória física de cada nó de trabalho no cluster do Hadoop.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

<!---HONumber=AcomDC_0406_2016-->