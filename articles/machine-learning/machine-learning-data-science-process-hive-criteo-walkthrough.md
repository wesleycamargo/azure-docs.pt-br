<properties 
	pageTitle="O Processo de Análise do Cortana em ação: usando clusters Hadoop do HDInsight no conjunto de dados da Criteo de 1 TB | Microsoft Azure" 
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
	ms.date="10/18/2015" 
	ms.author="ginathan;bradsev" />

# O Processo de Análises do Cortana em ação - Usando clusters Hadoop do Azure HDInsight em um conjunto de dados de 1 TB

Neste passo a passo, nós demonstramos totalmente o uso do Processo de Análise do Cortana em um [cluster Hadoop do Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, criar recursos e reduzir a resolução de dados de exemplo de um dos conjuntos de dados da [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponíveis publicamente. Usamos o Aprendizado de Máquina do Azure para criar um modelo de classificação binária nesses dados. Nós também mostramos como publicar um desses modelos como um serviço Web.

Também é possível usar um bloco de anotações do IPython para executar as tarefas apresentadas nesse passo a passo. Usuários que gostariam de testar essa abordagem devem consultar o tópico [Passo a passo da Criteo usando uma conexão ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).


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

1. [Criar uma conta de armazenamento](storage-whatis-account.md): esta conta de armazenamento é usada para armazenar dados no Armazenamento de Blob do Azure. Os dados usados em clusters do HDInsight são armazenados aqui.

2. [Personalizar clusters Hadoop do Azure HDInsight para ciência de dados](machine-learning-data-science-customize-hadoop-cluster.md): esta etapa cria um cluster Hadoop do Azure HDInsight com o Anaconda Python 2.7 de 64 bits instalado em todos os nós. Há duas etapas importantes (descritas neste tópico) a serem executadas para personalizar o cluster do HDInsight.

	* Você deve vincular a conta de armazenamento criada na etapa 1 ao cluster do HDInsight quando ele é criado. Essa conta de armazenamento é usada para acessar dados que podem ser processados dentro do cluster.

	* Você deve habilitar o Acesso Remoto ao nó principal do cluster após sua criação. Lembre das credenciais de acesso remoto que você especificar aqui (diferentes daqueles especificadas para o cluster durante sua criação): você precisará delas abaixo.

3. [Criar um espaço de trabalho do AM do Azure](machine-learning-create-workspace.md): este espaço de trabalho do Aprendizado de Máquina do Azure é usado para criar modelos de aprendizado de máquina após uma exploração de dados inicial e para reduzir a resolução no cluster do HDInsight.

## <a name="getdata"></a>Obter e consumir dados de uma fonte de pública

O conjunto de dados da [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) pode ser acessado clicando no link, aceitando os termos de uso e fornecendo um nome. Um instantâneo desse processo é mostrado abaixo:

![Aceitar os termos da Criteo](./media/machine-learning-data-science-process-hive-criteo-walkthrough/hLxfI2E.png)

Clique em **Continuar a Baixar** para saber mais sobre o conjunto de dados e sua disponibilidade.

Os dados residem em um local público do [armazenamento de blob do Azure](storage-dotnet-how-to-use-blobs.md): wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. O "wasb" refere-se ao local do Armazenamento de Blob do Azure.

1. Os dados desse armazenamento de blob público consistem de três subpastas de dados descompactados.
		
	1. A subpasta *raw/count/* contém os primeiros 21 dias de dados, de day\_00 a day\_20
	2. A subpasta *raw/train/* consiste de um dia de dados, day\_21
	3. A subpasta *raw/test/* consiste de dois dias de dados, day\_22 e day\_23

2. Para quem quiser começar com os dados no gzip brutos, eles também estão disponíveis na pasta principal, *raw/*, como day\_NN.gz, em que NN vai de 00 a 23.

Uma abordagem alternativa para acessar, explorar e modelar esses dados que não requer nenhum download local é explicada mais adiante neste passo a passo, quando criamos tabelas de Hive.

## <a name="login"></a>Faça logon no nó principal do cluster

Para fazer logon no nó principal do cluster, use o portal de [Gerenciamento do Azure](manage.windowsazure.com) para localizar o cluster. Clique no ícone de elefante do HDInsight à esquerda e depois clique duas vezes no nome do cluster. Navegue até a guia **Configuration**, clique duas vezes no ícone CONNECT na parte inferior da página e insira suas credenciais de acesso remoto quando solicitado. Isso leva ao nó principal do cluster.

Um típico primeiro logon no nó principal do cluster é semelhante ao seguinte:

![Fazer logon no cluster](./media/machine-learning-data-science-process-hive-criteo-walkthrough/Yys9Vvm.png)


À esquerda, vemos a "Linha de Comando do Hadoop", que será onde fazemos o trabalho pesado de exploração de dados. Nós também vemos duas URLs úteis - "Status do Hadoop Yarn" e "Nó de Nome do Hadoop". A URL do status de yarn mostra o andamento do trabalho e a URL de nó de nome fornece detalhes sobre a configuração do cluster.

Agora estamos prontos para começar a primeira parte do passo a passo: explorar dados usando o Hive e preparar dados para o Aprendizado de Máquina do Azure.

## <a name="hive-db-tables"></a> Criar tabelas e banco de dados do Hive

Para criar tabelas de Hive para nosso conjunto de dados da Criteo, abra a ***Linha de Comando do Hadoop*** na área de trabalho do nó principal e entre no diretório do Hive digitando o comando

    cd %hive_home%\bin

**OBSERVAÇÃO IMPORTANTE**: **execute todos os comandos do Hive neste passo a passo no prompt do diretório bin/ do Hive acima. Isso solucionará automaticamente quaisquer problemas de caminho. Nós usaremos os termos "Prompt do diretório do Hive", "prompt do diretório bin/ do Hive" e "Linha de Comando do Hadoop" alternadamente.**

**OBSERVAÇÃO IMPORTANTE 2**: **para executar qualquer consulta de Hive, sempre é possível fazer o seguinte** cd %hive\_home%\\bin hive

Após a REPL do Hive aparecer com um sinal "hive>", basta recortar e colar a consulta para executá-la.

O código abaixo cria um banco de dados "criteo" e gera quatro tabelas:


* uma *tabela para gerar contagens* criada nos dias dia\_00 a dia\_20, 
* uma *tabela para ser usada como o conjunto de dados de treinamento* criada no dia\_21 e 
* duas *tabelas para serem usadas como os conjuntos de dados de teste* criadas nos dias dia\_22 e dia\_23, respectivamente. 

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

Por fim, examinamos o número de exemplos de teste do conjunto de dados de teste com base no day\_23.

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

Nós podemos usar função nativa do Hive "histogram\_numeric" para descobrir como é a distribuição das variáveis numéricas. O conteúdo de [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) é este:

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

Outro aspecto que é de interesse com relação às variáveis numéricas é o cálculo de percentuais aproximados. O "percentile\_approx" nativo do Hive faz isso para nós. O conteúdo de [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) é:

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

O script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) faz isso para dados de teste, day\_22:

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


Por fim, o script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) faz isso para dados de teste, day\_23:

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

Agora, nós estamos prontos para criar modelos no Estúdio do Aprendizado de Máquina do Azure. Nossos dados reduzidos são salvos como tabelas de Hive do cluster. Nós usaremos o módulo de **Leitor** do Aprendizado de Máquina do Azure para ler esses dados. As credenciais para acessar a conta de armazenamento deste cluster estão abaixo.

### <a name="step1"></a> Etapa 1: passar dados de tabelas do Hive para o Aprendizado de Máquina do Azure usando o módulo de Leitor e selecioná-los para um experimento de aprendizado de máquina

Comece selecionando **+NOVO**->**EXPERIMENTO**->**Experimento em branco**. Depois, na caixa **Pesquisar** na parte superior esquerda, procure "Leitor". Arraste e solte o módulo **Leitor** na tela do experimento (a parte central da tela) para usar o módulo para acesso a dados.

Esta é a aparência do **Leitor** ao obter dados da tabela do Hive:

![Leitor obtém dados](./media/machine-learning-data-science-process-hive-criteo-walkthrough/i3zRaoj.png)

Para o módulo **Leitor**, os valores dos parâmetros que são fornecidos no gráfico são somente exemplos do tipo de valores que você precisará fornecer. Aqui estão algumas diretrizes gerais sobre como preencher o conjunto de parâmetros para o módulo **Leitor**.

1. Escolha "Consulta de Hive" para **Fonte de dados**
2. Na caixa **Consulta ao banco de dados Hive**, um simples SELECT * FROM <nome\_banco\_dados.nome\_tabela> - é suficiente.
3. **URI do servidor Hcatalog**: se seu cluster for "abc", ele será somente: https://abc.azurehdinsight.net
4. **Nome da conta de usuário do Hadoop**: o nome de usuário escolhido no momento da programação do cluster. (NÃO o nome de usuário do Acesso Remoto!)
5. **Senha da conta de usuário do Hadoop**: a senha para o nome de usuário acima escolhido no momento da programação do cluster. (NÃO a senha de Acesso Remoto!)
6. **Local dos dados de saída**: escolha "Azure"
7. **Nome da conta de armazenamento do Azure**: a conta de armazenamento associada ao cluster
8. **Chave da conta de armazenamento do Azure**: a chave da conta de armazenamento associada ao cluster.
9. **Nome do contêiner do Azure**: se o nome do cluster for "abc", normalmente costuma ser apenas "abc". 


Após o **Leitor** terminar de obter os dados (você vê a marca de seleção verde no Módulo), salve-os dados como um Conjunto de dados (com um nome de sua escolha). A aparência é a seguinte:

![Leitor salva dados](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oxM73Np.png)

Clique com o botão direito do mouse na porta de saída do módulo de **Leitor**. Isso revela uma opção **Salvar como conjunto de dados** e uma opção **Visualizar**. A opção **Visualizar**, se clicada, exibe 100 linhas de dados, juntamente com um painel à direita que é útil para ver algumas estatísticas resumidas. Para salvar os dados, basta selecionar **Salvar como conjunto de dados** e seguir as instruções.

Para selecionar o conjunto de dados salvo para uso em um experimento de aprendizado de máquina, localize os conjuntos de dados usando a caixa **Pesquisa** mostrada abaixo. Em seguida, basta digitar parcialmente o nome do conjunto de dados para acessá-lo e arrastar o conjunto de dados para o painel principal. Soltá-lo no painel principal o seleciona para uso na modelagem do aprendizado de máquina.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/cl5tpGw.png)

***OBSERVAÇÃO IMPORTANTE:*** **faça isso para os conjuntos de dados de treinamento e de teste. Além disso, lembre-se de usar o nome do banco de dados e das tabelas que você atribuiu para essa finalidade. Os valores usados na figura são somente para fins de ilustração.**
 
### <a name="step2"></a> Etapa 2: criar um experimento simples no Estúdio de Aprendizado de Máquina do Azure para predizer cliques/nenhum clique

Nosso experimento do AM do Azure se parece com o seguinte:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xRpVfrY.png)

Agora, vamos examinar os principais componentes nesse experimento. Como lembrete, primeiro precisamos arrastar nosso treinamento salvo e os conjuntos de dados de teste para a nossa tela de experimento.

#### Limpar dados ausentes

O módulo **Limpar dados ausentes** faz o que seu nome diz: limpa dados ausentes de maneiras que podem ser especificadas pelo usuário. Examinando este módulo, vemos isso:

![Limpar dados ausentes](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0ycXod6.png)

Aqui, optamos por substituir todos os valores ausentes por um 0. Existem outras opções que podem ser vistas examinando os menus suspensos no módulo.

#### Engenharia de recurso nos dados

Pode haver milhões de valores exclusivos para alguns recursos categóricos de grandes conjuntos de dados. Usar métodos simples como codificação one-hot para representar recursos categóricos altamente dimensionais é totalmente impraticável. Neste passo a passo, demonstramos como usar recursos de contagem usando módulos internos de Aprendizado de Máquina do Azure para gerar representações compactas dessas variáveis categóricas de grande dimensão. O resultado final é um tamanho de modelo menor, tempos de treinamento mais rápidos e métricas de desempenho que são muito semelhantes a outras técnicas.

##### Criando transformações de contagem

Para criar recursos de contagem, usamos o módulo **Criar transformação de contagem** que está disponível no Aprendizado de Máquina do Azure. O módulo tem esta aparência:


![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/e0eqKtZ.png) ![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OdDN0vw.png)


**Observação importante**: na caixa **Contar colunas**, inserimos as colunas nas quais desejamos realizar contagens. Geralmente, essas são (conforme mencionado) colunas categóricas altamente dimensionais. No início, mencionamos que o conjunto de dados Criteo tem 26 colunas categóricas: de Col15 para Col40. Aqui, podemos contar todas elas e dar a elas seus índices (de 15 a 40 separados por vírgulas, como mostrado).

Para usar o módulo no modo MapReduce (apropriado para grandes conjuntos de dados), precisamos de acesso a um cluster HDInsight Hadoop (aquele usado para a exploração de recurso acima também pode ser reutilizado para essa finalidade) e suas credenciais. Os valores acima ilustram como os valores preenchidos aparentam (substitua os valores fornecidos como ilustração com aqueles relevantes para seu próprio uso e caso).

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/05IqySf.png)

Na figura acima, vamos mostrar como inserir o local do blob de entrada. Esse local tem os dados reservados para a criação de tabelas de contagem.


Após a execução desse módulo, podemos salvar a transformação para mais tarde clicando com botão direito do mouse no módulo e selecionando a opção **Salvar como Transformação**:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IcVgvHR.png)

Em nossa arquitetura de experimento mostrada acima, o conjunto de dados "ytransform2" corresponde exatamente a uma transformação de contagem salva. Para o restante desse experimento, assumimos que o leitor usou um módulo **Compilar transformação de contagem** em alguns dados para gerar contagens e usá-las para gerar recursos de contagem em conjuntos de dados de treinamento e de teste.

##### Escolhendo quais recursos de contagem incluir como parte dos conjuntos de dados de treinamento e de teste

Assim que tivermos uma transformação de contagem pronta, o usuário pode escolher quais recursos incluir nos seus conjuntos de dados de treinamento e de teste usando o módulo **Modificar parâmetros da tabela de contagem**. Vamos mostrar esse módulo abaixo para fins de conclusão, mas, para manter a simplicidade, não use de fato no nosso experimento.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/PfCHkVg.png)

Nesse caso, como se vê, optamos por usar somente os log-possibilidades e ignorar a coluna de retirada. Também podemos definir parâmetros, como o limite da lixeira, quantos exemplos pseudo anteriores adicionar para suavização e se o ruído Laplaciano deve ser usado ou não. Todos esses são recursos avançados e deve-se observar que os valores padrão são um bom ponto de partida para usuários não familiarizados com esse tipo de geração de recurso.

##### Transformação de dados antes de gerar os recursos de contagem

Agora vamos nos concentrar em um ponto importante sobre como transformar nossos dados de treinamento e de teste antes de realmente gerar recursos de contagem. Observe que há dois módulos **Executar Script R** usados antes de podermos aplicar a transformação de contagem aos nossos dados.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/aF59wbc.png)

Veja o primeiro script R:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/3hkIoMx.png)


Nesse script R, renomeamos nossas colunas para nomes "Col1" a "Col40". Isso ocorre porque a transformação de contagem espera nomes nesse formato.

No segundo script R, podemos equilibrar a distribuição entre classes positivas e negativas (classes 1 e 0, respectivamente) reduzindo a classe negativa. O script R abaixo mostra como fazer isso:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/91wvcwN.png)

Nesse script R simples, usamos "pos\_neg\_ratio" para definir a quantidade de equilíbrio entre as classes positiva e negativa. Isso é importante, já que melhorar o desequilíbrio de classe normalmente tem benefícios de desempenho para problemas de classificação em que a distribuição de classe está distorcida (lembre-se de que em nosso caso, temos classes positivo 3,3% e 96,7% negativo).

##### Aplicando a transformação de contagem aos nossos dados

Por fim, podemos usar o módulo **Aplicar transformação** para aplicar as transformações de contagem aos nossos conjuntos de dados de treino e de teste. Este módulo usa a transformação da contagem salva como uma entrada e os conjuntos de dados de treinamento ou de teste como outra entrada e retorna dados com recursos de contagem. Isso é mostrado abaixo:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xnQvsYf.png)

##### Um trecho de como os recursos de contagem se parecem

É instrutivo observar a aparência dos recursos de contagem em nosso caso. A seguir, mostramos um trecho disso:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/FO1nNfw.png)

Nesse trecho, mostramos que para as colunas que pudemos contar, obtivemos as contagens e possibilidades de log além de qualquer retirada relevante.

Agora estamos prontos para criar um modelo de Aprendizado de Máquina do Azure usando esses conjuntos de dados transformados. Na próxima seção, vamos mostrar como isso pode ser feito.

#### Criação de modelo de Aprendizado de Máquina do Azure

##### Opção do aprendiz

Primeiro precisamos escolher um aprendiz. Vamos usar uma árvore de decisão aumentada, de duas classes, como nosso aprendiz. Aqui estão as opções padrão para esse aprendiz:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/bH3ST2z.png)

Para nosso experimento, vamos simplesmente escolher os valores padrão. Podemos observar que os padrões são geralmente significativos e uma boa maneira de se obter linhas de base rápidas sobre o desempenho. Se desejar, depois de ter uma linha de base, você pode melhorar o desempenho varrendo parâmetros.

#### Treinar o modelo

Para obter treinamento, podemos simplesmente invocar um módulo **Modelo de treinamento**. As duas entradas para ele são o aprendiz Árvore de Decisão Aumentada de Duas Classes e nosso conjunto de dados de treinamento. Isso é mostrado abaixo:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/2bZDZTy.png)


#### Pontuar o modelo

Assim que tivermos um modelo treinado, estamos prontos para avaliar o conjunto de dados de teste e seu desempenho. Podemos fazer isso usando o módulo **Modelo de pontuação** mostrado abaixo, junto com um módulo **Avaliar modelo**:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/fydcv6u.png)


### <a name="step5"></a> Etapa 5: Avaliar o modelo

Por fim, gostaríamos de analisar o desempenho do modelo. Normalmente, para problemas de classificação de duas classes (binária), uma boa medida é o AUC. Para visualizá-lo, nós vinculamos o módulo **Modelo de classificação** a um módulo **Modelo de avaliação**. Clicar em **Visualizar** no módulo **Modelo de avaliação** produz um gráfico semelhante ao abaixo:

![Avaliar o modelo de BDT do módulo](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0Tl0cdg.png)

Em problemas de classificação binária (ou de duas classes), uma boa medida da precisão de previsão é o AUC (área abaixo da curva). Abaixo, mostramos nossos resultados usando esse modelo em nosso conjunto de dados de teste. Para obtê-los, clique com o botão direito do mouse na porta de saída do módulo **Modelo de avaliação** e selecione **Visualizar**.

![Módulo Visualizar modelo de avaliação](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step6"></a> Etapa 6: Publicar o modelo como um serviço Web a ser consumido
A capacidade de publicar um modelo de Aprendizado de Máquina do Azure como serviços Web com um nível mínimo de confusão é um recurso valioso para torná-lo amplamente disponível. Depois disso, qualquer pessoa pode fazer chamadas para o serviço Web com dados de entrada para os quais precisem de previsões, e o serviço Web usa o modelo para retornar as previsões.

Para fazer isso, primeiro salvamos nosso modelo treinado como um objeto de Modelo Treinado. Isso é feito clicando com o botão direito do mouse no módulo **Modelo de treinamento** e usando a opção **Salvar como um Modelo Treinado**.

Em seguida, precisamos criar portas de entrada e saída para nosso serviço Web:

* uma porta de entrada usa dados da mesma forma como os dados para os quais precisamos de previsões 
* uma porta de saída retorna os Rótulos Pontuados e as probabilidades associadas.

#### Selecionar algumas linhas de dados para a porta de entrada

Podemos, de maneira conveniente, usar uma **Transformação do tipo Apply SQL** para selecionar apenas 10 linhas para servirem como dados de porta de entrada. Selecione somente essas linhas de dados para a nossa porta de entrada usando a consulta SQL mostrada abaixo.

![Dados na porta de entrada](./media/machine-learning-data-science-process-hive-criteo-walkthrough/XqVtSxu.png)

#### Serviço Web
Agora estamos prontos para executar um experimento pequeno que pode ser usado para publicar nosso serviço Web.

#### Gerar dados de entrada para o serviço Web

Como etapa inicial, como a tabela de contagem é grande, pegamos algumas linhas de dados de teste e geramos dados de saída por meio delas com recursos de contagem. Isso pode servir como o formato de dados de entrada para nosso serviço Web. Isso é mostrado abaixo:

![Criar dados de entrada de BDT](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OEJMmst.png)

Observação: para o formato de dados de entrada, usaremos a SAÍDA do módulo **Recursos de contagem**. Após o fim da execução do experimento, nós salvamos a saída do módulo **Recursos de contagem** como um conjunto de dados.

**Observação importante:** este Conjunto de dados será usado para os dados de entrada do serviço Web.

#### Experimento de pontuação publicação do serviço Web

Primeiro, mostramos sua aparência. A estrutura essencial é um módulo do **Modelo de Pontuação** que aceita o nosso objeto de modelo treinado e algumas linhas de dados de entrada que geramos nas etapas anteriores usando o módulo **Recursos de contagem**. Usamos "Colunas do Projeto" para projetar os Rótulos pontuados e as Probabilidades de pontuação.

![Colunas do projeto](./media/machine-learning-data-science-process-hive-criteo-walkthrough/kRHrIbe.png)

Observe como o módulo **Projetar colunas** pode ser usado para 'filtrar' dados de um conjunto de dados. Nós mostramos o conteúdo abaixo:

![Filtrando com o módulo Colunas do projeto](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oVUJC9K.png)

Para obter as portas de entrada e de saída azuis, basta clicar em **preparar serviço Web** no canto inferior direito. Executar esse experimento também nos permite publicar o serviço Web clicando no ícone **PUBLICAR SERVIÇO WEB** no canto inferior direito, mostrado abaixo.

![Publicar serviço Web](./media/machine-learning-data-science-process-hive-criteo-walkthrough/WO0nens.png)


Após o serviço Web ser publicado, somos redirecionados para uma página parecida com esta:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/YKzxAA5.png)

Vemos dois links para serviços Web no lado esquerdo:

* O **REQUEST/RESPONSE** Service (ou RRS) é destinado a previsões únicas e é o que utilizaremos neste workshop. 
* O serviço **EXECUÇÃO EM LOTES** (BES) é usado para previsões em lotes e exige que os dados de entrada usados para fazer previsões residam no Armazenamento de Blob do Azure.

Clicar no link **SOLICITAÇÃO/RESPOSTA** nos leva a uma página que nos fornece um código gravado previamente em C#, Python e R. Esse código pode ser usado de modo conveniente para fazer chamadas para o serviço Web. Observe que a chave da API nesta página deve ser usada para autenticação.

É conveniente copiar esse código python para uma nova célula no bloco de anotações IPython.

Abaixo, mostramos um segmento de código Python com a chave de API correta.

![Código Python](./media/machine-learning-data-science-process-hive-criteo-walkthrough/f8N4L4g.png)


Observe que substituímos a chave de API padrão com a chave de API de nossos serviços Web. Clicar em**Executar** nessa célula em um bloco de anotações IPython produz a seguinte resposta:

![Resposta do IPython](./media/machine-learning-data-science-process-hive-criteo-walkthrough/KSxmia2.png)

Podemos ver que para os dois exemplos de teste sobre os quais perguntamos (na estrutura JSON do script Python), recebemos respostas no formulário "Scored Labels, Scored Probabilities". Observe que, neste caso, escolhemos os valores padrão que o código predefinido fornece (0 para todas as colunas numéricas e a cadeia de caracteres "value" para todas as colunas categóricas).

Isso conclui nosso passo a passo total mostrando como lidar com o conjunto de dados de grande dimensão usando o Aprendizado de Máquina do Azure. Começamos com um terabyte de dados, construímos um modelo de previsão e o implantamos como um serviço Web na nuvem.

<!---HONumber=AcomDC_0128_2016-->