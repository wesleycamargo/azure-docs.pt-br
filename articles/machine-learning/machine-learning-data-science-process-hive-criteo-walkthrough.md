<properties 
	pageTitle="Processo e Tecnologia de Análises Avançadas em Ação: usando clusters Hadoop em um conjunto de dados da Criteo de 1 TB | Microsoft Azure" 
	description="Usando o ADAPT (Processo e Tecnologia de Análises Avançadas) para um cenário de ponta a ponta empregando um cluster Hadoop do HDInsight para criar e implantar um modelo usando um grande conjunto de dados (1 TB) disponível publicamente" 
	services="machine-learning,hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="ginathan;mohabib;bradsev" />

# Processo e Tecnologia de Análises Avançadas em Ação - Usando Clusters Hadoop do Azure HDInsight em um conjunto de dados de 1 TB

Neste passo a passo, nós demonstramos o uso do ADAPT (Processo e Tecnologia de Análises Avançadas) ponta a ponta com um [cluster Hadoop do Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, criar recursos e reduzir a resolução de dados de exemplo de um dos conjuntos de dados da [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponíveis publicamente. Nós usamos o Aprendizado de Máquina do Azure para criar modelos de regressão e classificação binária utilizando esses dados. Nós também mostramos como publicar um desses modelos como um serviço Web.

Também é possível usar um bloco de anotações do iPython para executar as tarefas apresentadas neste passo a passo. Usuários que gostariam de testar essa abordagem devem consultar o tópico [Passo a passo da Criteo usando uma conexão ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).


## <a name="dataset"></a>Descrição do conjunto de dados da Criteo

O conjunto de dados da Criteo é um conjunto de dados de previsão de cliques que contém cerca de 370 GB de arquivos TSV comprimidos em gzip (cerca de 1,3 TB de arquivos não comprimidos), totalizando mais de 4,3 bilhões de registros. Ele é obtido a partir de 24 dias de cliques de dados disponibilizados pela [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Para a conveniência dos cientistas de dados, nós descompactamos os dados disponibilizados para fazermos os experimentos.

Cada registro deste conjunto de dados contém 40 colunas:

- a primeira coluna é uma coluna de rótulos que indica se o usuário clica em um anúncio (valor 1) ou não clica (valor 0) 
- as 13 colunas seguintes são numéricas e 
- as últimas 26 colunas são colunas categóricas 

As colunas são anônimas e usam uma série de nomes enumerados: "Col1" (para a coluna de rótulos) a "Col40" (para a última coluna categórica).

Este é um trecho das 20 primeiras colunas de duas observações (linhas) desse conjunto de dados:

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20	

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Há valores ausentes nas colunas numéricas e categóricas do conjunto de dados. Nós descrevemos abaixo um método simples para lidar com os valores ausentes. Detalhes adicionais dos dados são exploradas abaixo, quando os armazenamos em tabelas do Hive.

**Definição:** *CTR (taxa de clickthrough):* é o percentual de cliques nos dados. Neste conjunto de dados da Criteo, a CTR é de cerca de 3,3% ou 0,033.

## <a name="mltasks"></a>Exemplos de tarefas de previsão
Dois exemplos de problemas de previsão são abordados neste passo a passo:

1. **Classificação binária**: prevê se um usuário clicou ou não em um anúncio:
	- Classe 0: não clicou
	- Classe 1: clicou

2. **Regressão**: prevê a probabilidade de um clique no anúncio por meio de características do usuário.


## <a name="setup"></a>Configurar um cluster Hadoop do HDInsight para ciência de dados

**Observação:** normalmente, esta é uma tarefa para o **Administrador**.

Configure seu ambiente de Ciência de dados do Azure para a criação de soluções analíticas de previsão com clusters do HDInsight em três etapas:

1. [Criar uma conta de armazenamento](../storage-whatis-account.md): esta conta de armazenamento é usada para armazenar dados no Armazenamento de Blob do Azure. Os dados usados em clusters do HDInsight são armazenados aqui.

2. [Personalizar clusters Hadoop do Azure HDInsight para ciência de dados](machine-learning-data-science-customize-hadoop-cluster.md): esta etapa cria um cluster Hadoop do Azure HDInsight com o Anaconda Python 2.7 de 64 bits instalado em todos os nós. Há duas etapas importantes (descritas neste tópico) a serem executadas para personalizar o cluster do HDInsight.

	* Você deve vincular a conta de armazenamento criada na etapa 1 ao cluster do HDInsight quando ele é criado. Essa conta de armazenamento é usada para acessar dados que podem ser processados dentro do cluster.

	* Você deve habilitar o Acesso Remoto ao nó principal do cluster após sua criação. Lembre das credenciais de acesso remoto que você especificar aqui (diferentes daqueles especificadas para o cluster durante sua criação): você precisará delas abaixo.

3. [Criar um espaço de trabalho do AM do Azure](machine-learning-create-workspace.md): este espaço de trabalho do Aprendizado de Máquina do Azure é usado para criar modelos de aprendizado de máquina após uma exploração de dados inicial e para reduzir a resolução no cluster do HDInsight.

## <a name="getdata"></a>Obter e consumir dados de uma fonte de pública

O conjunto de dados da [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) pode ser acessado clicando no link, aceitando os termos de uso e fornecendo um nome. Um instantâneo desse processo é mostrado abaixo:

![Aceitar os termos da Criteo](http://i.imgur.com/hLxfI2E.png)

Clique em **Continuar a Baixar** para saber mais sobre o conjunto de dados e sua disponibilidade.

Os dados residem em um local público do [armazenamento de blob do Azure](../storage-dotnet-how-to-use-blobs.md): wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. O "wasb" refere-se ao local do Armazenamento de Blob do Azure.

1. Os dados desse armazenamento de blob público consistem de três subpastas de dados descompactados.
		
	1. A subpasta *raw/count/* contém os primeiros 21 dias de dados, de day_00 a day_20
	2. A subpasta *raw/train/* consiste de um dia de dados, day_21
	3. A subpasta *raw/test/* consiste de dois dias de dados, day_22 e day_23

2. Para quem quiser começar com os dados no gzip brutos, eles também estão disponíveis na pasta principal, *raw/*, como day_NN.gz, em que NN vai de 00 a 23.

Uma abordagem alternativa para acessar, explorar e modelar esses dados que não requer nenhum download local é explicada mais adiante neste passo a passo, quando criamos tabelas de Hive.

## <a name="login"></a>Faça logon no nó principal do cluster

Para fazer logon no nó principal do cluster, use o portal de [Gerenciamento do Azure](manage.windowsazure.com) para localizar o cluster. Clique no ícone de elefante do HDInsight à esquerda e depois clique duas vezes no nome do cluster. Navegue até a guia **Configuration**, clique duas vezes no ícone CONNECT na parte inferior da página e insira suas credenciais de acesso remoto quando solicitado. Isso leva ao nó principal do cluster.

Um típico primeiro logon no nó principal do cluster é semelhante ao seguinte:

![Fazer logon no cluster](http://i.imgur.com/Yys9Vvm.png)

À esquerda, vemos a "Linha de Comando do Hadoop", que será onde fazemos o trabalho pesado de exploração de dados. Nós também vemos duas URLs úteis - "Status do Hadoop Yarn" e "Nó de Nome do Hadoop". A URL do status de yarn mostra o andamento do trabalho e a URL de nó de nome fornece detalhes sobre a configuração do cluster.

Agora estamos prontos para começar a primeira parte do passo a passo: explorar dados usando o Hive e preparar dados para o Aprendizado de Máquina do Azure.

## <a name="hive-db-tables"></a> Criar tabelas e banco de dados do Hive

Para criar tabelas de Hive para nosso conjunto de dados da Criteo, abra a ***Linha de Comando do Hadoop*** na área de trabalho do nó principal e entre no diretório do Hive digitando o comando

    cd %hive_home%\bin

**OBSERVAÇÃO IMPORTANTE**: **execute todos os comandos do Hive neste passo a passo no prompt do diretório bin/ do Hive acima. Isso solucionará automaticamente quaisquer problemas de caminho. Nós usaremos os termos "Prompt do diretório do Hive", "prompt do diretório bin/ do Hive" e "Linha de Comando do Hadoop" alternadamente.**

**OBSERVAÇÃO IMPORTANTE 2**: **para executar qualquer consulta de Hive, sempre é possível fazer o seguinte** cd %hive_home%\bin hive

Após a REPL do Hive aparecer com um sinal "hive>", basta recortar e colar a consulta para executá-la.

O código abaixo cria um banco de dados "criteo" e gera quatro tabelas:


* uma *tabela de contagem* baseada nos dias day_00 a day_20, 
* uma *tabela de treinamento* baseada no dia day_21 e 
* duas *tabelas de teste* baseadas nos dias day_22 e day_23, respectivamente. 

Nós dividimos nosso conjunto de dados de teste em duas tabelas diferentes porque um dos dias é feriado, e queremos determinar se o modelo é capaz de detectar diferenças entre dias que são feriado e dias que não são, com base na taxa de clickthrough.

O script [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) é exibido abaixo para fins de conveniência:

	CREATE DATABASE IF NOT EXISTS criteo;
	DROP TABLE IF EXISTS criteo.criteo_count;
	CREATE TABLE criteo.criteo_count (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

	DROP TABLE IF EXISTS criteo.criteo_train;
	CREATE TABLE criteo.criteo_train (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

	DROP TABLE IF EXISTS criteo.criteo_test_day_22;
	CREATE TABLE criteo.criteo_test_day_22 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

	DROP TABLE IF EXISTS criteo.criteo_test_day_23;
	CREATE TABLE criteo.criteo_test_day_23 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Observe que todas essas tabelas são externas, uma vez que apenas apontamos para locais do Armazenamento de Blob do Azure (wasb).

**Há duas maneiras de executar QUALQUER consulta de Hive que mencionamos agora.**

1. **Usando a linha de comando REPL do Hive**: a primeira maneira é emitir um comando "hive" e copiar e colar a consulta acima na linha de comando REPL do Hive. Para fazer isto:

		cd %hive_home%\bin
		hive

 	Agora na linha de comando REPL, recortar e colar a consulta a executa.

2. **Salvando consultas em um arquivo e executando o comando**: a segunda maneira é salvar as consultas em um arquivo .hql ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e emitir o seguinte comando para executar a consulta:

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### Confirme a criação do banco de dados e da tabela

Em seguida, confirmamos a criação do banco de dados emitindo o comando abaixo no prompt do diretório bin/ do Hive:

		hive -e "show databases;"

Isso fornece:

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

Isso confirma a criação do novo banco de dados, "criteo".

Para ver quais tabelas foram criadas, basta emitirmos o comando abaixo no prompt do diretório bin/ do Hive:

		hive -e "show tables in criteo;"

Você verá a saída a seguir:

		criteo_count
		criteo_test_day_22
		criteo_test_day_23
		criteo_train
		Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a>Exploração de dados no Hive

Agora, estamos prontos para fazer algumas explorações de dados básicas no Hive. Vamos começar pela contagem do número de exemplos nas tabelas de dados de treinamento e teste.

### Número de exemplos de treinamento

O conteúdo de [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) é mostrado abaixo:

		SELECT COUNT(*) FROM criteo.criteo_train;

Isso resulta em:

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

Também é possível emitir o comando abaixo no prompt do diretório bin/ do Hive:

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### Número de exemplos de teste nos dois conjuntos de dados de teste

Agora, nós contamos o número de exemplos nos dois conjuntos de dados de teste. O conteúdo de [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) é mostrado abaixo:

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Isso resulta em:
	
		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

Como de costume, também podemos chamar o script no prompt do diretório bin/ do Hive emitindo o comando abaixo:

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Por fim, examinamos o número de exemplos de teste do conjunto de dados de teste com base no day_23.

O comando para fazer isso é semelhante ao acima (consulte [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

		SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Isso fornece:
	
		178274637
		Time taken: 253.089 seconds, Fetched: 1 row(s)

### Distribuição de rótulos no conjunto de dados de treinamento

A distribuição de rótulos no conjunto de dados de treinamento é de interesse. Para ver isso, nós mostramos o conteúdo de [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

		SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Isso gera a distribuição de rótulos:

		1       6292903
		0       185922280
		Time taken: 459.435 seconds, Fetched: 2 row(s)

Observe que o percentual de rótulos positivos é de cerca de 3,3% (consistente com o conjunto de dados original).
		
### Distribuições de histograma de algumas variáveis numéricas no conjunto de dados de treinamento

Nós podemos usar função nativa do Hive "histogram_numeric" para descobrir como é a distribuição das variáveis numéricas. O conteúdo de [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) é este:

		SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
			(SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
			) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Isso produz o seguinte:

		26      155878415
		2606    92753
		6755    22086
		11202   6922
		14432   4163
		17815   2488
		21072   1901
		24113   1283
		27429   1225
		30818   906
		34512   723
		38026   387
		41007   290
		43417   312
		45797   571
		49819   428
		53505   328
		56853   527
		61004   160
		65510   3446
		Time taken: 317.851 seconds, Fetched: 20 row(s)

A combinação de EXIBIÇÃO LATERAL -explodir no Hive serve para produzir uma saída semelhante ao SQL em vez da lista comum. Observe que, na tabela acima, a primeira coluna corresponde ao centro do compartimento e a segunda à frequência do compartimento.

### Percentis aproximados de algumas variáveis numéricas no conjunto de dados de treinamento

Outro aspecto que é de interesse com relação às variáveis numéricas é o cálculo de percentuais aproximados. O "percentile_approx" nativo do Hive faz isso para nós. O conteúdo de [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) é:

		SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Isso resulta em:

		1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
		Time taken: 564.953 seconds, Fetched: 1 row(s)

Observe que, normalmente, a distribuição de percentis está estreitamente relacionada à distribuição de histograma de qualquer variável numérica.

### Encontre o número de valores exclusivos para algumas colunas categóricas no conjunto de dados de treinamento

Continuando a exploração de dados, nós encontramos agora, para algumas colunas categóricas, o número de valores exclusivos que elas levam. Para fazer isso, mostramos o conteúdo de [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

		SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Isso resulta em:

		19011825
		Time taken: 448.116 seconds, Fetched: 1 row(s)

Observe que Col15 tem valores exclusivos em 19M! Usar técnicas simples como codificação "one-hot" para codificar variáveis categóricas altamente dimensionais deste tipo é inviável. Em particular, nós explicamos e demonstramos uma técnica poderosa e robusta chamada [Aprendizado com contagens](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para lidar com esse problema de modo eficaz.

Finalizamos esta subseção examinando também o número de valores exclusivos para algumas outras colunas categóricas. O conteúdo de [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) é:

		SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
		COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
		FROM criteo.criteo_train;

Isso resulta em:

		30935   15200   7349    20067   3
		Time taken: 1933.883 seconds, Fetched: 1 row(s)

Mais uma vez vemos que, com exceção de Col20, todas as colunas têm muitos valores exclusivos.

### Contagens de ocorrências conjuntas de pares de variáveis categóricas no conjunto de dados de treinamento

As contagens de ocorrências conjuntas de pares de variáveis categóricas no conjunto de dados de treinamento também é de interesse. Isso pode ser determinado usando o código em [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

		SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Neste caso, ordenamos as contagens de modo inverso e examinamos as 15 primeiras. Isso nos fornece:

		ad98e872        cea68cd3        8964458
		ad98e872        3dbb483e        8444762
		ad98e872        43ced263        3082503
		ad98e872        420acc05        2694489
		ad98e872        ac4c5591        2559535
		ad98e872        fb1e95da        2227216
		ad98e872        8af1edc8        1794955
		ad98e872        e56937ee        1643550
		ad98e872        d1fade1c        1348719
		ad98e872        977b4431        1115528
		e5f3fd8d        a15d1051        959252
		ad98e872        dd86c04a        872975
		349b3fec        a52ef97d        821062
		e5f3fd8d        a0aaffa6        792250
		265366bf        6f5c7c41        782142
		Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Reduzir a resolução de conjuntos de dados para o Aprendizado de Máquina do Azure

Após termos explorado os conjuntos de dados e demonstrado como podemos fazer esse tipo de exploração para quaisquer variáveis (incluindo combinações), agora nós reduzimos a resolução dos conjuntos de dados para que possamos criar modelos no Aprendizado de Máquina do Azure. Lembre-se de que o problema em que nos concentramos é: dado um conjunto de atributos de exemplo (valores de recursos da Col2 à Col40), podermos prever se Col1 é 0 (sem cliques) ou 1 (com clique).

Para reduzir nossos conjuntos de dados de treinamento e teste para 1% do tamanho original, usamos a função nativa RAND() do Hive. O script seguinte, [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) faz isso para o conjunto de dados de treinamento:

		CREATE TABLE criteo.criteo_train_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		---Now downsample and store in this table

		INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Isso resulta em:

		Time taken: 12.22 seconds
		Time taken: 298.98 seconds

O script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) faz isso para dados de teste, day_22:

		--- Now for test data (day_22)

		CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Isso resulta em:

		Time taken: 1.22 seconds
		Time taken: 317.66 seconds


Por fim, o script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) faz isso para dados de teste, day_23:

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Isso resulta em:

		Time taken: 1.86 seconds
		Time taken: 300.02 seconds

Com isso, nós estamos prontos para usar nossos conjuntos de dados de treinamento e teste reduzidos para criar modelos no Aprendizado de Máquina do Azure.

Há um componente final importante antes de passarmos para o Aprendizado de Máquina do Azure, que é relacionado à tabela de contagem. Na próxima sub-seção, discutiremos este componente detalhadamente.

##<a name="count"></a> Uma breve discussão sobre a tabela de contagem

Como vimos, diversas variáveis categóricas têm uma dimensionalidade muito alta. Em nosso passo a passo, nós apresentamos uma técnica poderosa chamada [Aprendizado com contagens](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para codificar essas variáveis de maneira eficiente e robusta. Mais informações sobre essa técnica são disponibilizadas no link fornecido.

**Observação:** neste passo a passo, nós nos concentramos no uso de tabelas de contagem para produzir representações compactas de recursos categóricos de alta dimensionalidade. Certamente, esta não é a única maneira de codificar recursos categóricos. Para saber mais sobre outras técnicas, usuários interessados podem conferir [codificação one-hot](http://en.wikipedia.org/wiki/One-hot) e [hash de recursos](http://en.wikipedia.org/wiki/Feature_hashing).

Para criar tabelas de contagem com os dados de contagem, usamos os dados na pasta raw/count. Na seção de modelagem, mostramos aos usuários como criar essas tabelas de contagem para recursos categóricos do zero ou como usar uma tabela de contagem criada previamente para seus explorações. A seguir, quando falamos em "tabelas criadas previamente", nos referimos ao uso das tabelas de contagem que fornecemos. Abaixo, há instruções detalhadas sobre como acessar essas tabelas.

## <a name="aml"></a> Criar um modelo com o Aprendizado de Máquina do Azure

Nosso processo de criação de modelo no Aprendizado de Máquina do Azure seguirá estas etapas:

1. [Obter os dados de tabelas do Hive no Aprendizado de Máquina do Azure](#step1)
2. [Criar o experimento: limpar os dados, escolher um aprendiz e criar recursos com tabelas de contagem](#step2)
3. [Treinar o modelo](#step3)
4. [Pontuar o modelo com base nos dados de teste](#step4)
5. [Avaliar o modelo](#step5)
6. [Publicar o modelo como um serviço Web a ser consumido](#step6)

Agora, nós estamos prontos para criar modelos no Estúdio do Aprendizado de Máquina do Azure. Nossos dados reduzidos são salvos como tabelas de Hive do cluster. Nós usaremos o módulo de Leitor do Aprendizado de Máquina do Azure para ler esses dados. As credenciais para acessar a conta de armazenamento deste cluster estão abaixo.

### <a name="step1"></a> Etapa 1: passar dados de tabelas do Hive para o Aprendizado de Máquina do Azure usando o módulo de Leitor e selecioná-los para um experimento de aprendizado de máquina

Para o módulo de **Leitor**, os valores dos parâmetros definidos no gráfico são apenas exemplos. Aqui estão orientações gerais para “preencher” os parâmetros definidos para o módulo de **Leitor**.

1. Escolha "Hive query" como Fonte de dados
2. Em "Hive query", um simples SELECT * FROM <nome_banco_dados.nome_tabela> - é suficiente.
3. URI do servidor Hcatalog: se seu cluster for "abc", ele é: https://abc.azurehdinsight.net
4. Nome da conta de usuário do Hadoop: nome de usuário escolhido no momento da preparação do cluster (NÃO é o nome de usuário do Acesso Remoto)
5. Senha da conta de usuário do Hadoop: senha do nome de usuário acima, escolhida no momento da preparação do cluster (NÃO é a senha do Acesso Remoto)
6. Local dos dados de saída: escolha "Azure"
7. Nome da conta de armazenamento do Azure: a conta de armazenamento associada ao cluster
8. Chave da conta de armazenamento do Azure: chave da conta de armazenamento associada ao cluster
9. Nome do contêiner do Azure: se o nome do cluster for "abc", normalmente costuma ser apenas "abc" 

Esta é a aparência do **Leitor** ao obter dados da tabela do Hive:

![Leitor obtém dados](http://i.imgur.com/i3zRaoj.png)

Após o **Leitor** terminar de obter os dados (você vê a marca de seleção verde no Módulo), salve-os dados como um Conjunto de dados (com um nome de sua escolha). A aparência é a seguinte:

![Leitor salva dados](http://i.imgur.com/oxM73Np.png)


Clique com o botão direito do mouse na porta de saída do módulo de **Leitor**. Isso revela uma opção **Salvar como conjunto de dados** e uma opção **Visualizar**. A opção **Visualizar**, se clicada, exibe 100 linhas de dados, juntamente com um painel à direita que é útil para ver algumas estatísticas resumidas. Para salvar os dados, basta selecionar **Salvar como conjunto de dados** e seguir as instruções.

Para selecionar o conjunto de dados salvo para uso em um experimento de aprendizado de máquina, localize os conjuntos de dados usando a **Pesquisa** mostrada abaixo. Em seguida, basta digitar o nome do conjunto de dados parcialmente para acessá-lo e arrastar o conjunto de dados para o painel principal. Soltá-lo no painel principal o seleciona para uso na modelagem do aprendizado de máquina.

![Localizar conjunto de dados](http://i.imgur.com/rx4nnUH.png)

Faça isso para os conjuntos de dados de treinamento e de teste.

### <a name="step2"></a> Etapa 2: criar um experimento simples no Estúdio de Aprendizado de Máquina do Azure para predizer cliques/nenhum clique

Primeiro, nós mostramos uma arquitetura de experimento simples e depois detalhamos um pouco alguns aspectos específicos. Primeiro, limpamos os dados, depois escolhemos um aprendiz e, por fim, mostramos como criar recursos com tabelas de contagem criadas previamente ou construídas do zero pelo usuário.

![Arquitetura do experimento](http://i.imgur.com/R4iTLYi.png)

Para fazer uma busca detalhada, começamos com nossos conjuntos de dados salvos, conforme mostrado.

O módulo **Limpar dados ausentes** faz o que seu nome diz: limpa dados ausentes de maneiras que podem ser especificadas pelo usuário. Examinando este módulo, vemos isso:

![Limpar dados ausentes](http://i.imgur.com/0ycXod6.png)

Aqui, optamos por substituir todos os valores ausentes por um 0. Existem outras opções que podem ser vistas examinando os menus suspensos no módulo.

Em seguida, escolhemos um aprendiz. Vamos usar uma árvore de decisão aumentada, de duas classe, como nosso aprendiz. Em particular, nós mostraremos como usar recursos de contagem em recursos categóricos alta dimensionais para criar representações compactas do nosso modelo e também para fazer testes e treinamentos eficientes.

Um parênteses aqui para discutir o que as tabelas de contagem realmente são: elas são contagens condicionais de classe (às vezes, nós as chamamos apenas de "contagens condicionais"). Essencialmente, elas são uma forma de contar os valores de um recurso para cada classe e usar essas contagens para calcular probabilidades de logs.


#### Obter acesso às tabelas de contagem criadas previamente para modelagem

Para obter acesso a nossas tabelas de contagem criadas previamente, clique em **Galeria**, conforme mostrado abaixo:

![Galeria](http://i.imgur.com/TsWkig3.png)

Clicar em **Galeria** leva o usuário a uma página semelhante à seguinte:

![Página principal da Galeria](http://i.imgur.com/dmXo0KR.png)

Aqui, pesquise pela expressão "criteo counts" e role para baixo na lista de resultados. Você deverá ver:

![Contagens da Criteo](http://i.imgur.com/JZ119Jf.png)

Clicar neste experimento leva a uma página semelhante à seguinte:

![Contagens](http://i.imgur.com/dxdjMjh.png)

Aqui, clique em **Abrir no estúdio** para copiar o experimento para seu espaço de trabalho. Isso também copia automaticamente os conjuntos de dados – nesse caso, os dois conjuntos de dados principais são a tabela de contagem e os metadados de contagem, que descrevemos com mais detalhes.

#### Recursos de contagem no conjunto de dados

Os módulos seguintes de nosso experimento envolvem o uso de tabelas de contagem criadas previamente. Para usar as tabelas de contagem criadas previamente, pesquise por "cr_count_" na guia de Pesquisa de um novo experimento e você verá dois conjuntos de dados: "cr_count_cleanednulls_metadata" e "cr_count_table_cleanednulls". Arraste e solte ambos no painel do experimento à direita. Clicar com o botão direito nas portas de saída, como sempre, nos permite visualizar sua aparência.

A visualização dos metadados da tabela de contagem é semelhante à seguinte:

![Metadados da tabela de contagem](http://i.imgur.com/A39PIe7.png)

Observe que os metadados contêm informações sobre em quais colunas baseamos as contagens condicionais, se usamos um dicionário para compilá-la (uma alternativa é o esboço de contagem mínima), a propagação de hash usada, o número de bits de hash usados para fazer o hash dos recursos, o número de classes e assim por diante.

A visualização da tabela de contagem é semelhante à seguinte:

![Tabela de contagem](http://i.imgur.com/NJn1EQO.png)

Podemos ver que a tabela de contagem contém informações sobre as contagens condicionais de classe. O valor dos recursos categóricos está em "Valor de Hash", de modo que os recursos são transformados em hash.

Como os recursos de contagem são incorporados aos conjuntos de dados? Para isso, nós usamos o módulo de **Recursos** de contagem, como mostrado abaixo:

![Módulo Recursos de contagem](http://i.imgur.com/dnMdrR1.png)

Após a tabela de contagem ser criada (lembre-se de que estamos produzindo contagens condicionais de classe de recursos categóricos aqui), usamos o módulo **Recursos de contagem** mostrado acima para incorporar esses recursos de contagem ao nosso conjunto de dados. Como podemos ver, o módulo **Recursos** permite escolher quais recursos contar, se precisamos apenas das probabilidades de log ou também das contagens e outras opções avançadas.

#### Criar uma tabela de contagem do zero

Lembre-se de que mencionamos em "Uma breve discussão sobre a tabela de contagem" que, além de usar tabelas de contagem criadas previamente (o que discutimos detalhadamente em seções anteriores), os usuários também podem criar suas próprias tabelas de contagem do zero.

Nesta seção, demonstramos como criar uma tabela de contagem do zero. Para isso, usamos o módulo **Criar tabela de contagem** mostrado abaixo com suas configurações:

![Módulo Criar tabela de contagem](http://i.imgur.com/r7pP8Qq.png)

A última parte das configurações para este módulo estão a seguir:

![Configurações do módulo Criar tabela de contagem](http://i.imgur.com/PvmSh3C.png)


**Observação importante:** para as configurações de conta de armazenamento e de cluster, use os SEUS valores relevantes!

Clicar em **Executar** nos permite criar as tabelas de contagem no cluster escolhido. A saída é, como mostrado anteriormente, a tabela de contagem e seus metadados associados. Com essas tabelas prontas, agora podemos criar o experimento.


### <a name="step3"></a> Etapa 3: Treinar o modelo

Para selecionar esta opção, basta digitar "two class boosted" na caixa de Pesquisa e arrastar o módulo. Usamos os valores padrão para o aprendiz de árvore de decisão aumentada, mostrados abaixo:

![Aprendiz BDT](http://i.imgur.com/dDk0Jtv.png)

Precisamos de três componentes finais antes de executar o experimento de AM.

O primeiro é um módulo de Treinamento de Modelo. Enquanto sua primeira porta usa o aprendiz como entrada, a segunda usa o conjunto de dados de treinamento para aprendizado. Mostramos abaixo sua aparência e depois mostramos quais parâmetros precisam ser definidos no módulo.

![Conexões do módulo Modelo de treinamento de BDT](http://i.imgur.com/szS2xBb.png)

![Configurações do módulo Modelo de treinamento de BDT](http://i.imgur.com/nd7lHBL.png)

### <a name="step4"></a> Etapa 4: Pontuação do modelo em um conjunto de dados de teste

O segundo componente é uma maneira de pontuar o conjunto de dados de teste. Isso é feito de maneira conveniente usando o módulo **Modelo de classificação** - ele usa como entrada o modelo aprendido com os dados de treinamento, bem como o conjunto de dados de teste para basear as previsões. Abaixo, mostramos sua aparência.

![Conexões do modelo BDT de classificação](http://i.imgur.com/AwIH1rH.png)

### <a name="step5"></a> Etapa 5: Avaliar o modelo

Por fim, gostaríamos de ver o desempenho do modelo. Normalmente, para problemas de classificação de duas classes (binária), uma boa medida é o AUC. Para visualizá-lo, nós vinculamos o módulo " Modelo de classificação" a um módulo "Modelo de avaliação". Clicar em **Visualizar** no módulo **Modelo de avaliação** produz um gráfico semelhante ao abaixo:

![Avaliar o modelo de BDT do módulo](http://i.imgur.com/0Tl0cdg.png)

Em problemas de classificação binária (ou de duas classes), uma boa medida da precisão de previsão é o AUC. Abaixo, mostramos nossos resultados usando esse modelo em nosso conjunto de dados de teste. Para obtê-los, clique com o botão direito do mouse na porta de saída do módulo **Modelo de avaliação** e selecione **Visualizar**.

![Módulo Visualizar modelo de avaliação](http://i.imgur.com/IRfc7fH.png)

### <a name="step6"></a> Etapa 6: Publicar o modelo como um serviço Web a ser consumido
É de grande interesse a capacidade de poder publicar modelos do aprendizado de máquina serviços Web. Depois de fazer isso, podemos fazer chamadas para o serviço Web usando dados para os quais precisamos de previsões e o modelo idealmente retornaria uma previsão de algum tipo.

Para fazer isso, primeiro salvamos nosso modelo treinado como um objeto de Modelo Treinado. Isso é feito clicando com o botão direito do mouse no módulo **Modelo de treinamento** usando a opção "Salvar como um Modelo Treinado”.

Em seguida, queremos criar uma porta de entrada e saída para nosso serviço Web – uma porta de entrada que recebe dados na mesma forma dos dados para os quais precisamos de previsões e uma porta de saída que retorna os Rótulos de pontuação e as probabilidades associadas.

#### Selecionar algumas linhas de dados para a porta de entrada

Agora, mostramos como selecionar apenas algumas linhas de dados para nossa porta de entrada.

![Dados na porta de entrada](http://i.imgur.com/XqVtSxu.png)

Podemos, de maneira conveniente, usar uma transformação do tipo Apply SQL para selecionar apenas 10 linhas para servirem como dados de porta de entrada.

#### Serviço Web
Agora estamos prontos para executar um experimento pequeno que pode ser usado para publicar nosso serviço Web.

#### Gerar dados de entrada para o serviço Web

Como etapa inicial, como a tabela de contagem é grande, pegamos algumas linhas de dados de teste e geramos dados de saída por meio delas com recursos de contagem. Isso serve como o formato de dados de entrada para nosso serviço Web. Isso é mostrado abaixo:

![Criar dados de entrada de BDT](http://i.imgur.com/OEJMmst.png)

Observação: para o formato de dados de entrada, usaremos a SAÍDA do módulo **Recursos de contagem**. Após o fim da execução do experimento, nós salvamos a saída do módulo **Recursos de contagem** como um conjunto de dados. **Observação importante:** este Conjunto de dados será usado para os dados de entrada do serviço Web.

#### Experimento de pontuação publicação do serviço Web

Primeiro, mostramos sua aparência. A estrutura essencial é um módulo do modelo de Pontuação que aceita o nosso objeto de modelo treinado e algumas linhas de dados de entrada que geramos nas etapas anteriores usando o módulo **Recursos de contagem**. Usamos "Colunas do Projeto" para projetar os Rótulos pontuados e as Probabilidades de pontuação.

![Colunas do projeto](http://i.imgur.com/kRHrIbe.png)

É instrutivo observar como o módulo Colunas do Projeto pode ser usado para “filtrar” os dados de um conjunto de dados. Nós mostramos o conteúdo abaixo:

![Filtrando com o módulo Colunas do projeto](http://i.imgur.com/oVUJC9K.png)

Para obter as portas de entrada e saída azuis, simplesmente clicamos em "prepare webservice" no canto inferior direito. Executar esse experimento também nos permite publicar o serviço Web clicando no ícone **PUBLICAR SERVIÇO WEB** no canto inferior direito, mostrado abaixo.

![Publicar serviço Web](http://i.imgur.com/WO0nens.png)

Após o serviço Web ser publicado, somos redirecionados para uma página parecida com esta:

![](http://i.imgur.com/YKzxAA5.png)

Vemos dois links para serviços Web no lado esquerdo:

* O **REQUEST/RESPONSE** Service (ou RRS) é destinado a previsões únicas e é o que utilizaremos neste workshop. 
* O **BATCH EXECUTION** Service (BES), como o nome indica, é usado para previsões em lote e requer que os dados com base nos quais a previsão será feita residam em um blob do Azure.

Clicar no link **SOLICITAÇÃO/RESPOSTA** nos leva a uma página que nos fornece um código gravado previamente em C#, Python e R. Esse código pode ser usado de modo conveniente para fazer chamadas para o serviço Web. Observe que a chave da API nesta página deve ser usada para autenticação.

É conveniente copiar esse código python para uma nova célula no iPython Notebook.

Abaixo, mostramos um segmento de código Python com a chave de API correta.

![Código Python](http://i.imgur.com/f8N4L4g.png)

Observe que substituímos a chave de API padrão pela chave de API de nossos Web. Clicar em "Run" nessa célula em um iPython Notebook gera a seguinte resposta:

![Resposta de iPython](http://i.imgur.com/KSxmia2.png)

Podemos ver que para os dois exemplos de teste sobre os quais perguntamos (na estrutura JSON do script Python), recebemos respostas no formulário "Scored Labels, Scored Probabilities". Observe que, neste caso, escolhemos os valores padrão que o código predefinido fornece (0s para todas as colunas numéricas, a cadeia de caracteres "value" para todas as colunas categóricas).

Isso conclui nossa discussão sobre como lidar com conjuntos de dados em grande escala de ponta a ponta usando o AM do Azure - fomos de um terabyte de dados até um modelo de previsão implantado como um serviço Web na nuvem.

 

<!---HONumber=July15_HO2-->