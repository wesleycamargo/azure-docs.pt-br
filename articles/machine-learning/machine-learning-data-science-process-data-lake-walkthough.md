<properties
	pageTitle="Ciência de Dados Escalonáveis no Azure Data Lake: um Passo a Passo de ponta a ponta | Microsoft Azure"
	description="Como usar o Azure Data Lake para realizar tarefas de classificação binária e de exploração de dados em um conjunto de dados."  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,wguo123"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="bradsev;weig"/>


# Ciência de dados escalonáveis no Azure Data Lake: um passo a passo de ponta a ponta

Este passo a passo mostra como usar o Azure Data Lake para exploração de dados e tarefas de classificação binária em uma amostra do conjunto de dados de tarifas e corridas de táxi de Nova York para prever se uma gorjeta será ou não paga por uma tarifa. Ele orienta você pelas etapas do [Processo de Ciência de Dados](http://aka.ms/datascienceprocess), de ponta a ponta, da aquisição de dados até o treinamento de modelo e, em seguida, para a implantação de um serviço Web que publica o modelo.

**Análise Azure Data Lake**

O [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) tem todos os recursos necessários para tornar mais fácil para os cientistas de dados a tarefa de armazenar dados de qualquer forma, velocidade e tamanho e para conduzir o processamento de dados, análise avançada e modelagem de aprendizado de máquina, com alta escalabilidade e de uma maneira econômica. Você paga por trabalho, somente quando os dados estão realmente sendo processados. A Análise Azure Data Lake inclui o U-SQL, uma linguagem de consulta que mescla a natureza declarativa simples e familiar do SQL com o poder expressivo do C# para fornecer capacidade de consulta distribuída escalonável. Ele permite que você processe dados não estruturados aplicando o esquema na leitura, na inserção de lógica personalizada e em UDFs, e inclui extensibilidade para habilitar o controle refinado sobre como executar em grande escala. Para saber mais sobre a filosofia de design por trás do U-SQL, consulte [postagem de blog do Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

A Análise Data Lake também é um componente importante do Cortana Analytics Suite e funciona com o Azure SQL Data Warehouse, Power BI e Data Factory. Isso fornece uma plataforma de nuvem completa de análise avançada e big data.

Este passo a passo começa descrevendo os pré-requisitos e os recursos que são necessários para concluir as tarefas com o Análise Data Lake que formam o processo de ciência de dados e como instalá-los. Em seguida, ele descreve as etapas desse processo realizado usando U-SQL e Aprendizado de Máquina do Azure e como usar o Python para realizar tarefas semelhantes.


**U-SQL e Aprendizado de Máquina do Azure**

Este passo a passo usa o Visual Studio para editar scripts U-SQL para processar o conjunto de dados. Os scripts U-SQL são descritos aqui e fornecidos em um arquivo separado. O processo inclui ingestão, exploração e amostragem dos dados. Então, ele mostra como executar um trabalho com script U-SQL no portal do Azure. Tabelas de hive são criadas para os dados em um cluster HDInsight associado para facilitar a compilação e implantação de um modelo de classificação binária no Estúdio de Aprendizado de Máquina do Azure.


**Python**

Este passo a passo também contém uma seção que mostra como executar essas tarefas de ciência de dados usando o Python em uma amostra conjunto de dados de tarifas e corridas de táxi de Nova York. Nós fornecemos um bloco de notas Jupyter com os scripts Python para cada uma das etapas neste processo. O bloco de notas inclui código para algumas etapas adicionais de engenharia de recursos e construção de modelos, como modelagem de regressão e classificação multiclasse, além do modelo de classificação binária descrito aqui. A tarefa de regressão consiste em prever o valor da gorjeta com base em outros recursos de gorjeta.


**Scripts**

Somente as etapas principais são descritas neste passo a passo. Você pode baixar o **script U-SQL** completo e o **Bloco de notas Jupyter** do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).


## Pré-requisitos

Antes de começar esses tópicos, você deve ter o seguinte:

- Uma assinatura do Azure. Se ainda não tiver uma, veja [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- [Recomendado] Visual Studio 2013 ou 2015. Se você ainda não tiver uma dessas versões instaladas, você poderá baixar uma Community edition gratuita [aqui](https://www.visualstudio.com/visual-studio-homepage-vs.aspx). Clique no botão **Baixar Community 2015** sob a seção do Visual Studio.

>[AZURE.NOTE] Em vez do Visual Studio, você também pode usar o Portal do Azure para enviar consultas do Azure Data Lake. Forneceremos instruções sobre como fazê-lo, tanto com o Visual Studio quanto no portal, na seção intitulada **Processar dados com o U-SQL**.

- Inscrever-se para o Azure Data Lake Preview

>[AZURE.NOTE] Você precisa obter aprovação para usar o ADLS (Repositório Azure Data Lake) e o ADLA (Análise Azure Data Lake), já que esses serviços estão em preview. Quando você criar seu primeiro ADLS ou ADLA, será solicitado que você se inscreva. Para se inscrever, clique em **Inscrever-se para preview**, leia o contrato e clique em **OK**. Aqui, por exemplo, é a página de inscrição do ADLS:

 ![2](./media/machine-learning-data-science-process-data-lake-walkthough/ADLA-preview-signup.PNG)
 


## Preparar ambiente de ciência de dados para o Azure Data Lake
Para preparar o ambiente de ciência de dados para este passo a passo, crie os seguintes recursos:

- ADLS (Repositório Azure Data Lake) 
- ADLA (Análise Azure Data Lake)
- Conta de armazenamento de Blobs do Azure
- Conta do Estúdio de Aprendizado de Máquina do Azure
- Ferramentas do Azure Data Lake para Visual Studio (Recomendado)

Esta seção fornece instruções sobre como criar cada um desses recursos. Observe que o Repositório Azure Data Lake pode ser criado separadamente ou quando você criar o Análise Azure Data Lake como o armazenamento padrão; da mesma forma, a conta de armazenamento de Blobs do Azure pode ser criada separadamente ou como o armazenamento padrão quando você cria um Cluster Linux HDInsight. As instruções são referenciadas para criar cada um desses recursos separadamente, a seguir, mas as duas contas de armazenamento não precisam ser criadas em etapas separadas.

### Criar um Repositório Azure Data Lake

Crie um ADLS do [Portal do Azure](http://ms.portal.azure.com). Para detalhes, consulte [Criar um cluster HDInsight com o Repositório Data Lake usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de configurar a Identidade AAD do Cluster na folha **Fonte de Dados** da folha **Configuração Opcional** ali descrita.

 ![3](./media/machine-learning-data-science-process-data-lake-walkthough/create_ADLS.PNG)


### Criar uma conta da Análise Azure Data Lake
Crie uma conta da ADLA do [Portal do Azure](http://ms.portal.azure.com). Para detalhes, consulte [Tutorial: introdução à Análise Azure Data Lake usando o Portal do Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/machine-learning-data-science-process-data-lake-walkthough/create_ADLA_new.PNG)


### Criar uma conta de Armazenamento de Blobs do Azure
Crie sua conta de Armazenamento de Blobs do Azure do [Portal do Azure](http://ms.portal.azure.com). Para detalhes, veja a seção Criar uma conta de armazenamento em [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).
	
 ![5](./media/machine-learning-data-science-process-data-lake-walkthough/Create_Azure_Blob.PNG)


### Configurar uma conta do Estúdio de Aprendizado de Máquina do Azure
Inscrever-se/entrar no Estúdio de Aprendizado de Máquina do Azure da página [Aprendizado de Máquina do Azure](https://azure.microsoft.com/services/machine-learning/). Clique no botão **Comece agora mesmo** e, em seguida, escolha um "Espaço de Trabalho Gratuito" ou "Espaço de Trabalho Padrão". Depois disso, você poderá criar experimentos no Estúdio AM do Azure.

### Instalar Ferramentas do Azure Data Lake 
Instale as Ferramentas do Azure Data Lake para sua versão do Visual Studio das [Ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![10](./media/machine-learning-data-science-process-data-lake-walkthough/install_ADL_tools_VS.PNG)

Após a instalação ser concluída com êxito, abra o Visual Studio. Você deve ver a guia Data Lake no menu na parte superior. Os recursos do Azure devem aparecer no painel esquerdo quando você entra na sua conta do Azure.

 ![11](./media/machine-learning-data-science-process-data-lake-walkthough/install_ADL_tools_VS_done.PNG)


## O conjunto de dados Corridas de Táxi de NYC
O conjunto de dados usado aqui é um conjunto de dados disponível publicamente, o conjunto de dados [Corridas de Táxi de NYC](http://www.andresmh.com/nyctaxitrips/). Os dados de Corridas de Táxi de NYC são formados por cerca de 20 GB de arquivos CSV compactados (aproximadamente 48 GB descompactados) que incluem mais de 173 milhões de corridas individuais, com tarifas pagas por cada corrida. Cada registro de corrida inclui o local e o horário de saída e chegada, o número da carteira de habilitação do taxista anônimo e o número de medalhão (identificador exclusivo do táxi). Os dados abrangem todas as corridas no ano de 2013 e são fornecidos nos dois conjuntos de dados a seguir para cada mês:

 - O CSV contém ‘trip\_data’ detalhes da corrida, como o número de passageiros, pontos de saída e chegada, duração e quilometragem da corrida. Aqui estão alguns exemplos de registros:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

 - O CSV ‘trip\_fare’ contém detalhes sobre as tarifas pagas em cada corrida, como tipo de pagamento, valor da tarifa, custos adicionais e impostos, gorjetas e pedágios, e o valor total pago. Aqui estão alguns exemplos de registros:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para unir trip\_data e trip\_fare é composta pelos três campos a seguir: medallion, hack\_license e pickup\_datetime. Os arquivos CSV brutos podem ser acessados de um blob de armazenamento do Azure público.

## Processar dados com U-SQL

As tarefas de processamento de dados ilustradas nesta seção incluem ingestão, verificação de qualidade, exploração e amostragem dos dados. Também vamos mostrar como unir tabelas de corrida e de tarifa. A seção final mostra a execução de um trabalho com script U-SQL no portal do Azure. Eis os links para cada subseção:

- [Ingestão de dados: dados de leitura de blob público](#ingest)
- [Verificações de qualidade de dados](#quality)
- [Exploração de dados](#explore)
- [Unir tabelas de corrida e tarifa](#join)
- [Amostragem de dados](#sample)
- [Executar trabalhos com U-SQL](#run)

Os scripts U-SQL são descritos aqui e fornecidos em um arquivo separado. Você pode baixar os **scripts U-SQL** completos do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Para executar o U-SQL, abra o Visual Studio, clique em **Arquivo --> Novo --> Projeto**, escolha **Projeto U-SQL**, nomeie-o e salve-o em uma pasta.

![12](./media/machine-learning-data-science-process-data-lake-walkthough/create_USQL_project.PNG)

>[AZURE.NOTE] Se em vez do Visual Studio você estiver usando o Portal do Azure para executar U-SQL, você poderá navegar para o recurso do Análise Azure Data Lake e seguir essas etapas ilustradas para enviar consultas.


![29](./media/machine-learning-data-science-process-data-lake-walkthough/portal_submit_job.PNG)

### <a name="ingest"></a>Ingestão de dados: dados de leitura de blob público

A localização dos dados no blob do Azure é referenciada como **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** e pode ser extraída usando **Extractors.Csv()**. Substitua seu próprio nome do contêiner e o nome da conta de armazenamento nos scripts a seguir para container_name@blob_storage_account_name no endereço wasb. Como os nomes de arquivo estão mesmo formato, podemos usar **trip\_data\_{*}.csv** para ler todos os 12 arquivos de corrida.

	///Read in Trip data
	@trip0 =
	    EXTRACT 
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Como há cabeçalhos na primeira linha, é necessário remover os cabeçalhos e alterar os tipos de coluna para aqueles apropriados. É possível salvar os dados processados no Armazenamento do Azure Data Lake usando **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ ou na conta de armazenamento de Blobs do Azure usando **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**.

	// change data types
	@trip =
	    SELECT 
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    DateTime.Parse(pickup_datetime) AS pickup_datetime,
	    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
	    Int32.Parse(passenger_count) AS passenger_count,
	    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
	    Double.Parse(trip_distance) AS trip_distance,
	    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
	    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
	    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
	    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

	////output data to ADL
	OUTPUT @trip   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_trip.csv"
	USING Outputters.Csv(); 

	////Output data to blob
	OUTPUT @trip   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
	USING Outputters.Csv();  

Da mesma forma, podemos realizar a leitura dos conjuntos de dados de tarifa. Clique com o botão direito do mouse em Repositório Azure Data Lake, você pode optar por examinar os dados em **Portal do Azure--> Explorador de Dados** ou no **Gerenciador de Arquivos**, dentro do Visual Studio.

 ![13](./media/machine-learning-data-science-process-data-lake-walkthough/data_in_ADL_VS.PNG)

 ![14](./media/machine-learning-data-science-process-data-lake-walkthough/data_in_ADL.PNG)


### <a name="quality"></a>Verificações de qualidade de dados

Após as tabelas de corrida e de tarifa terem sido lidas, as verificações de qualidade de dados podem ser feitas conforme descrito a seguir. Os arquivos CSV resultantes podem ser exportados para o armazenamento de Blobs do Azure ou o Repositório Azure Data Lake.

Localize o número de medalhões e o número individual dos medalhões:

	///check the number of medallions and unique number of medallions
	@trip2 =
	    SELECT
	    medallion,
	    vendor_id,
	    pickup_datetime.Month AS pickup_month
	    FROM @trip;
	
	@ex_1 =
	    SELECT
	    pickup_month, 
	    COUNT(medallion) AS cnt_medallion,
	    COUNT(DISTINCT(medallion)) AS unique_medallion
	    FROM @trip2
	    GROUP BY pickup_month;
	    OUTPUT @ex_1   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
	USING Outputters.Csv(); 

Localize os medalhões com mais de 100 corridas:

	///find those medallions that had more than 100 trips
	@ex_2 =
	    SELECT medallion,
	           COUNT(medallion) AS cnt_medallion
	    FROM @trip2
	    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
	    GROUP BY medallion
	    HAVING COUNT(medallion) > 100;
	    OUTPUT @ex_2   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
	USING Outputters.Csv(); 

Localize esses registros inválidos em termos de pickup\_longitude:

	///find those invalid records in terms of pickup_longitude
	@ex_3 =
	    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
	    FROM @trip
	    WHERE
	    pickup_longitude <- 90 OR pickup_longitude > 90;
	    OUTPUT @ex_3   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
	USING Outputters.Csv(); 

Encontre os valores ausentes para algumas variáveis:

	//check missing values
	@res =
	    SELECT *,
	           (medallion == null? 1 : 0) AS missing_medallion
	    FROM @trip;
	
	@trip_summary6 =
	    SELECT 
	        vendor_id,
	    SUM(missing_medallion) AS medallion_empty, 
	    COUNT(medallion) AS medallion_total,
	    COUNT(DISTINCT(medallion)) AS medallion_total_unique  
	    FROM @res
	    GROUP BY vendor_id;
	OUTPUT @trip_summary6
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
	USING Outputters.Csv();



### <a name="explore"></a>Exploração de dados

Podemos fazer alguma exploração de dados para obter uma melhor compreensão dos dados.

Localize a distribuição de corridas com e sem gorjeta:

	///tipped vs. not tipped distribution
	@tip_or_not =
	    SELECT *,
	           (tip_amount > 0 ? 1: 0) AS tipped
	    FROM @fare;
	
	@ex_4 =
	    SELECT tipped,
	           COUNT(*) AS tip_freq
	    FROM @tip_or_not
	    GROUP BY tipped;
	    OUTPUT @ex_4   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
	USING Outputters.Csv(); 

Localize a distribuição de valores de gorjeta com valores de corte: 0, 5, 10 e 20 dólares.

	//tip class/range distribution
	@tip_class =
	    SELECT *,
	           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
	    FROM @fare;
	@ex_5 =
	    SELECT tip_class,
	           COUNT(*) AS tip_freq
	    FROM @tip_class
	    GROUP BY tip_class;
	    OUTPUT @ex_5   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
	USING Outputters.Csv(); 

Localize as estatísticas básicas de distância de corrida:

	// find basic statistics for trip_distance
	@trip_summary4 =
	    SELECT 
	        vendor_id,
	        COUNT(*) AS cnt_row,
	        MIN(trip_distance) AS min_trip_distance,
	        MAX(trip_distance) AS max_trip_distance,
	        AVG(trip_distance) AS avg_trip_distance 
	    FROM @trip
	    GROUP BY vendor_id;
	OUTPUT @trip_summary4
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
	USING Outputters.Csv();

Localize os percentuais de distância de corrida:

	// find percentiles of trip_distance
	@trip_summary3 =
	    SELECT DISTINCT vendor_id AS vendor,
	                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
	    FROM @trip;
	   // group by vendor_id;
	OUTPUT @trip_summary3
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
	USING Outputters.Csv(); 


### <a name="join"></a>Unir tabelas de corrida e tarifa

Tabelas de corrida e tarifa podem ser unidas por medalhão, hack\_license e pickup\_time.

	//join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

	//// output to blob
	OUTPUT @model_data_full   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 

	////output data to ADL
	OUTPUT @model_data_full   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 


Para cada nível de contagem de passageiros, calcule o número de registros, valor médio da gorjeta, variação do valor da gorjeta, percentual de corridas com gorjeta.

	// contigency table
	@trip_summary8 =
	    SELECT passenger_count,
	           COUNT(*) AS cnt,
	           AVG(tip_amount) AS avg_tip_amount,
	           VAR(tip_amount) AS var_tip_amount,
	           SUM(tipped) AS cnt_tipped,
	           (float)SUM(tipped)/COUNT(*) AS pct_tipped
	    FROM @model_data_full
	    GROUP BY passenger_count;
	    OUTPUT @trip_summary8
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
	USING Outputters.Csv();


### <a name="sample"></a>Amostragem de dados

Primeiro, selecionamos aleatoriamente 0,1% dos dados da tabela unida:

	//random select 1/1000 data for modeling purpose
	@addrownumberres_randomsample =
	SELECT *,
	        ROW_NUMBER() OVER() AS rownum
	FROM @model_data_full;
	
	@model_data_random_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_randomsample
	WHERE rownum % 1000 == 0;
	
	OUTPUT @model_data_random_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
	USING Outputters.Csv(); 

Em seguida, fazemos a amostragem estratificada pela variável binária tip\_class:

	//stratified random select 1/1000 data for modeling purpose
	@addrownumberres_stratifiedsample =
	SELECT *,
	        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
	FROM @model_data_full;
	
	@model_data_stratified_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_stratifiedsample
	WHERE rownum % 1000 == 0;
	//// output to blob
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 
	////output data to ADL
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 


### <a name="run"></a>Executar trabalhos com U-SQL

Quando você terminar de editar scripts U-SQL, você pode enviá-los ao servidor usando sua conta do Análise Azure Data Lake. Clique em **Data Lake**, **Enviar Trabalho**, selecione sua **Conta de Análise**, escolha **Paralelismo** e clique no botão **Enviar**.

 ![15](./media/machine-learning-data-science-process-data-lake-walkthough/submit_USQL.PNG)

Quando o trabalho for compilado com êxito, o status do seu trabalho será exibido no Visual Studio para monitoramento. Depois que a execução do trabalho for encerrada, você pode até mesmo repetir o processo de execução de trabalho e localizar as etapas de afunilamento para melhorar a eficiência do seu trabalho. Você também pode ir para o Portal do Azure para verificar o status dos trabalhos com U-SQL.

 ![16](./media/machine-learning-data-science-process-data-lake-walkthough/USQL_running_v2.PNG)


 ![17](./media/machine-learning-data-science-process-data-lake-walkthough/USQL_jobs_portal.PNG)


Agora, você pode verificar os arquivos de saída no armazenamento de Blobs do Azure ou Portal do Azure. Usaremos os dados da amostra estratificada para nossa modelagem na próxima etapa.

 ![18](./media/machine-learning-data-science-process-data-lake-walkthough/U-SQL-output-csv.PNG)

 ![19](./media/machine-learning-data-science-process-data-lake-walkthough/U-SQL-output-csv-portal.PNG)

## Compilar e implantar modelos no Aprendizado de Máquina do Azure
Vamos demonstrar duas opções para você efetuar pull de dados no Aprendizado de Máquina do Azure. Na primeira opção, você usar os dados da amostra que são gravados em um Blob do Azure (da etapa **Amostragem de dados** acima) e usar o Python para compilar um modelo e implantá-lo no Aprendizado de Máquina do Azure. Na segunda opção, você pode consultar os dados no Azure Data Lake diretamente usando uma consulta de Hive. Escolhendo essa opção, faz-se necessário criar um novo cluster HDInsight ou usar um cluster HDInsight existente no qual as tabelas de Hive apontam para os dados no Armazenamento do Azure Data Lake. Discutiremos ambas as opções abaixo.

### Opção 1: usar o Python para compilar e implantar modelos de aprendizado de máquina

Agora, vamos compilar e implantar modelos de aprendizado de máquina usando o Python. Podemos usar o Python para ler os dados processados do armazenamento de Blobs do Azure (salvo com U-SQL na etapa **amostragem de dados** acima) e compilar modelos. Crie um Bloco de notas Jupyter em seu computador local ou Estúdio de Aprendizado de Máquina do Azure. Mostraremos neste artigo apenas as etapas de modelagem e implantação. O Bloco de notas Jupyter contém o código completo para explorar, visualizar dados e também para engenharia, modelagem e implantação de recursos.

#### Importar bibliotecas Python

Para executar o exemplo de Bloco de notas Jupyter ou o arquivo de script Python, os seguintes pacotes Python serão necessários. Se você estiver usando o serviço de Bloco de anotações do AM do Azure, esses pacotes terão sido pré-instalados.

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage.blob import BlobService
	import tables
	import time
	import zipfile
	import random
	import sklearn
	from sklearn.linear_model import LogisticRegression
	from sklearn.cross_validation import train_test_split
	from sklearn import metrics
	from __future__ import division
	from sklearn import linear_model
	from azureml import services


#### Ler dados do blob

- Cadeia de conexão   

		CONTAINERNAME = 'test1'
		STORAGEACCOUNTNAME = 'XXXXXXXXX'
		STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
		BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
		blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	
- Ler como texto

		t1 = time.time()
		data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
		t2 = time.time()
		print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

 ![29](./media/machine-learning-data-science-process-data-lake-walkthough/python_readin_csv.PNG)
 
- Adicionar nomes de colunas e separar colunas

		colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
		'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
		df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
	


- Alterar algumas colunas para formato numérico

		cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
		,'tipped','tip_class','rownum']
		for col in cols_2_float:
		    df1[col] = df1[col].astype(float)

#### Compilar modelos de aprendizado de máquina

Aqui, criamos um modelo de classificação binária para prever se uma corrida tem gorjeta ou não. No Bloco de notas Jupyter, você encontrará outros dois modelos: classificação multiclasse e modelos de regressão.

- Primeiro, precisamos criar variáveis fictícias que possam ser usadas em modelos scikit-learn

		df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
		df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')

- Criar um quadro de dados para a modelagem

		cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
		data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
		
		X = data.iloc[:,1:]
		Y = data.tipped

- Divisão 60-40 entre treinamento e teste

		X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)

- Regressão logística no conjunto de treinamento

		model = LogisticRegression()
		logit_fit = model.fit(X_train, Y_train)
		print ('Coefficients: \n', logit_fit.coef_)
		Y_train_pred = logit_fit.predict(X_train)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/py_logit_coefficient.PNG)

- Conjunto de dados de testes de pontuação

		Y_test_pred = logit_fit.predict(X_test)

- Calcular métricas de Avaliação

		fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
		print fpr_train, tpr_train, thresholds_train
		
		fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
		print fpr_test, tpr_test, thresholds_test
		
		#AUC
		print metrics.auc(fpr_train,tpr_train)
		print metrics.auc(fpr_test,tpr_test)
		
		#Confusion Matrix
		print metrics.confusion_matrix(Y_train,Y_train_pred)
		print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/py_logit_evaluation.PNG)


 
#### Compilar a API do Serviço Web e consumi-la no Python

Queremos colocar o modelo de aprendizado de máquina em operação após ele ter sido compilado. Aqui, usamos o modelo logístico binário como um exemplo. Certifique-se de que a versão do scikit-learn em seu computador local é 0.15.1. Você não precisa se preocupar com isso caso use o serviço Estúdio AM do Azure.

- Localize suas credenciais do espaço de trabalho nas configurações do Estúdio AM do Azure. No Estúdio de Aprendizado de Máquina do Azure, clique em **Configurações** --> **Nome** --> **Tokens de Autorização**. 

	![](./media/machine-learning-data-science-process-data-lake-walkthough/workspace_id.PNG)


		workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
		auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

- Criar um Serviço Web

		@services.publish(workspaceid, auth_token) 
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(int) #0, or 1
		def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
		    return logit_fit.predict(inputArray)

- Obter as credenciais do serviço Web

		url = predictNYCTAXI.service.url
		api_key =  predictNYCTAXI.service.api_key
		
		print url
		print api_key

		@services.service(url, api_key)
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(float)
		def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    pass

- Chame a API do serviço Web. Você precisa esperar de 5 a 10 segundos após a etapa anterior.

		NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/call_API.PNG)

### Opção 2: criar e implantar modelos diretamente no Aprendizado de Máquina do Azure

Você pode usar o Estúdio de Aprendizado de Máquina do Azure para ler dados diretamente do Repositório Azure Data Lake, criar um modelo e implantá-lo. Para que o Aprendizado de Máquina do Azure leia dados diretamente do Repositório Azure Data Lake, você precisa criar uma tabela de Hive. Para isso, um cluster HDInsight do Azure separado precisa ser provisionado, no qual é criada uma tabela de Hive apontando para o Repositório Azure Data Lake. As subseções a seguir demonstram como fazer isso.

#### Criar um Cluster HDInsight em Linux
Crie um Cluster HDInsight (Linux) por meio do [Portal do Azure](http://ms.portal.azure.com). Para obter detalhes, consulte Criar um cluster HDInsight com acesso à seção do Repositório Azure Data Lake em [Criar um cluster HDInsight com o Repositório Data Lake usando o Portal do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![6](./media/machine-learning-data-science-process-data-lake-walkthough/create_HDI_cluster.PNG)

#### Criar tabela de Hive no HDInsight

Agora podemos criar tabelas de Hive no cluster HDInsight usando os dados armazenados no Repositório Azure Data Lake na etapa anterior. As tabelas de Hive serão usadas no Estúdio de Aprendizado de Máquina do Azure na próxima etapa. Vá para o cluster HDInsight criado no início do passo a passo. Clique em **Configurações** --> **Propriedades** --> **Identidade AAD do Cluster** --> **Acesso ao ADLS**, certifique-se de que sua conta do Repositório Azure Data Lake seja adicionada à lista com direitos de leitura, gravação e execução.

 ![20](./media/machine-learning-data-science-process-data-lake-walkthough/HDI_cluster_add_ADLS.PNG)


Em seguida, clique em **Painel** ao lado do botão Configurações e uma janela se abrirá. Clique em **Exibição de Hive** no canto superior direito da página e você verá o **Editor de Consultas**.

 ![21](./media/machine-learning-data-science-process-data-lake-walkthough/HDI_dashboard.PNG)

 ![22](./media/machine-learning-data-science-process-data-lake-walkthough/Hive_Query_Editor_v2.PNG)


Cole os scripts de Hive a seguir para criar uma tabela. O local da fonte de dados está na referência do Repositório Azure Data Lake desta maneira: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

	CREATE EXTERNAL TABLE nyc_stratified_sample
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string,
	  payment_type string,
	  fare_amount string,
	  surcharge string,
	  mta_tax string,
	  tolls_amount string,
	  total_amount string,
	  tip_amount string,
	  tipped string,
	  tip_class string,
	  rownum string
	  )
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	LOCATION 'adl://cdsp.azuredatalakestore.net:443/nyctaxi_weig/demo_ex_9_stratified_1_1000_copy.csv';

Quando a execução da consulta for concluída, você verá os resultados deste modo:

 ![23](./media/machine-learning-data-science-process-data-lake-walkthough/Hive_Query_results.PNG)



#### Compilar e implantar modelos no Estúdio de Aprendizado de Máquina do Azure

Agora estamos prontos para prosseguir para a criação e implantação de modelo no Aprendizado de Máquina do Azure. Os dados estão prontos para serem usados nesses problemas de previsão com nossos dados de amostra: classificação binária (com ou sem gorjeta), multiclassificação (tip\_class) e regressão (tip\_amount). Aqui, ilustraremos como compilar e implantar um modelo de classificação binária no Estúdio de Aprendizado de Máquina do Azure.

1. Obtenha os dados no AM do Azure usando o módulo **Leitor**, disponível na seção **Entrada e Saída de Dados**. Para saber mais, consulte a página de referência do [módulo Leitor](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/).
2. Selecione **Consulta de Hive** como a **Fonte de dados** no painel **Propriedades**.
3. Cole o seguinte script de Hive no editor **Consulta de banco de dados de Hive**

    	select * from nyc_stratified_sample;

4. Insira o URI do cluster HDInsight (ele pode ser encontrado no Portal do Azure), as credenciais do Hadoop, localização dos dados de saída e os nomes do contêiner, da chave e da conta de armazenamento do Azure.

 ![24](./media/machine-learning-data-science-process-data-lake-walkthough/reader_module_v3.PNG)

Um exemplo de um experimento de classificação binária lendo dados da tabela de Hive é mostrado na figura abaixo.

 ![25](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp.PNG)

Depois que o teste for criado, clique em **Configurar o serviço Web** --> **Serviço Web Preditivo**

 ![26](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp_deploy.PNG)

Execute automaticamente o teste de pontuação criado e, quando ele for concluído, clique em **Implantar o Serviço Web**

 ![27](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp_deploy_web.PNG)

O painel do serviço Web será exibido em breve:

 ![28](./media/machine-learning-data-science-process-data-lake-walkthough/AML_web_api.PNG)


## Resumo

Para recapitular o que fizemos neste passo a passo:

- criado um ambiente de ciência de dados para compilar soluções de ponta a ponta escalonáveis no Azure Data Lake;
- obtido um grande conjunto de dados público pelas etapas canônicas do processo de ciência de dados usando U-SQL e Python, começando pela aquisição de dados, passando pelo treinamento de modelo e prosseguindo para a implantação do modelo como um serviço Web.

<!---HONumber=AcomDC_0525_2016-->