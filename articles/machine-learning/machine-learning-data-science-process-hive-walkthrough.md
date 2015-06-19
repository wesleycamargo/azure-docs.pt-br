<properties 
	pageTitle="Processo de Ciência de Dados do Azure em ação: usando clusters do Hadoop do HDInsight | Azure" 
	description="Processo de Ciência de Dados do Azure ponta a ponta usando um cluster do Hadoop do HDInsight para compilar e implantar um modelo usando um conjunto de dados disponível publicamente." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

                
# Processo de Ciência de Dados do Azure em ação: usando clusters do Hadoop do HDInsight

Neste tutorial, você seguirá o mapa do Processo de Ciência de Dados do Azure ponta a ponta usando um cluster do Hadoop do Azure HDInsight para compilar e implantar um modelo de trabalho com um conjunto de dados publicamente disponível, o [Viagens de Táxi de NYC](http://www.andresmh.com/nyctaxitrips/). 


## <a name="dataset"></a>Descrição do Conjunto de Dados de Viagens de Táxi de NYC

Os dados de Viagens de Táxi em NYC são cerca de 20 GB de arquivos compactados em valores separados por vírgulas (CSV) (cerca de 48 GB descompactados), que incluem mais de 173 milhões de viagens individuais e a tarifa paga por cada viagem.  Cada registro de viagem inclui o local e horário de saída e chegada, número de carteira de motorista de taxista anônimas e o número do medalhão (identificador exclusivo do táxi).  Os dados abrangem todas as viagens no ano de 2013 e são fornecidos nos dois seguintes conjuntos de dados para cada mês:

1. Os arquivos CSV 'trip_data' contém detalhes da viagem, como o número de passageiros, pontos de saída e chegada, duração e extensão da viagem.  Aqui estão alguns exemplos de registros:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Os arquivos CSV 'trip_fare' contém detalhes sobre as passagens pagas por cada viagem, tais como tipo de pagamento, valor da tarifa, custos adicionais e impostos, gorjetas e pedágios, e o valor total pago.  Aqui estão alguns exemplos de registros:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para ingressar trip_data e trip_fare é composta pelos campos: medallion, hack_licence e pickup_datetime.

Há 12 arquivos  'trip_data'viagem e 'trip_fare', respectivamente.  Nos nomes de arquivo, números de 1 a 12 representam os meses quando as viagens foram feitas. 

## <a name="mltasks"></a>Exemplos de tarefas de previsão
Ao abordar a dados, determinar o tipo de previsões que deseja fazer com base na sua análise ajuda a esclarecer as tarefas que você precisará incluir em seu processo.
Aqui estão três exemplos de problemas de previsão que serão abordados neste passo a passo cuja formulação se baseia no *tip_amount*:

1. **Classificação binária**:  Prever ou não se uma gorjeta foi paga por uma viagem, ou seja um *tip_amount* maior que U$ 0 é um exemplo de positivo, enquanto um *tip_amount* de U$ 0 é um exemplo de negativo.

2. **Classificação multiclasse**:  Para prever o intervalo de valores de gorjeta paga pela viagem.  Dividimos o *tip_amount* em cinco compartimentos ou classes:
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Tarefa de regressão**:  Prever o valor da gorjeta paga por uma viagem.  


## <a name="setup"></a>Configurar o Ambiente de Ciência de Dados do Azure

Como indicado no guia [Planejar seu ambiente](machine-learning-data-science-plan-your-environment.md), há várias abordagens que podem ser usadas ao trabalhar com o conjunto de dados de Viagens de Táxi de NYC no Azure:

- Trabalhar com os dados em blobs do Azure e depois modelá-los no Aprendizado de Máquina do Azure
- Carregar os dados para um banco de dados do SQL Server e depois modelá-los no Aprendizado de Máquina do Azure
- Carregar os dados nas tabelas Hive do HDInsight e modelá-las no Aprendizado de Máquina do Azure

Dependendo do tamanho do conjunto de dados, local da fonte de dados e o ambiente de destino do Azure selecionados, esse cenário é semelhante ao [Cenário #7:  Grande conjunto de dados em arquivos locais, destino de Hive em clusters de Hadoop do Azure HDInsight](../machine-learning-data-science-plan-sample-scenarios.md#largedbtohive).

Para configurar seu ambiente de Ciência de Dados do Azure para essa abordagem que usa clusters do Hadoop do Azure HDInsight e, mais especificamente, tabelas e consultas Hive para armazenar e processar os dados, conclua as etapas a seguir.

1. [Criar uma conta de armazenamento](../storage-whatis-account.md)

2. [Criar um espaço de trabalho de AM do Azure](machine-learning-create-workspace.md)

3. [Provisionar uma Ciência de Dados **Windows** Máquina Virtual](machine-learning-data-science-setup-virtual-machine.md).

	> [AZURE.NOTE] Os scripts de exemplo serão baixados para a máquina virtual de Ciência de Dados durante o processo de configuração de máquina virtual.  Quando o script pós-instalação da VM for concluído, os exemplos estarão na biblioteca de documentos da VM em *C:\Users<user_name>\Documents\Data Science Scripts*  Pastas neste exemplo são conhecidas como **Scripts de Exemplo** abaixo.  As consultas de Hive de exemplo estão contidas em arquivos com a extensão .hql na pasta **Scripts de Exemplo**.  Observe que o *<nome_de_usuário>* referido no caminho para essa pasta é o nome de logon do Windows da VM, não seu nome de usuário do Azure.

4. [Personalizar os Clusters de Hadoop do Azure HDInsight para Ciência de Dados](machine-learning-data-science-customize-hadoop-cluster.md).  Esta etapa criará um cluster do Hadoop do Azure HDInsight com Anaconda Python 2.7 de 64 bits instalado em todos os nós.  Depois que o cluster do Hadoop personalizado é criado, habilite o acesso remoto ao nó principal do cluster do Hadoop no Portal do Azure usando o procedimento descrito neste tópico de personalização.


## <a name="getdata"></a>Obter os dados de uma fonte de pública

Para obter o conjunto de dados [Viagens de Táxi de NYC](http://www.andresmh.com/nyctaxitrips/) do seu local público, você pode usar qualquer um dos métodos descritos em [Mover dados de e para o Armazenamento de Blobs do Azure](machine-learning-data-science-move-azure-blob.md) para copiar os dados para sua nova máquina virtual.

Para copiar os dados usando AzCopy:

1. Faça logon na sua VM (máquina virtual)

2. Crie um novo diretório no disco de dados da VM (Observação:  Não use o Disco Temporário que vem com a VM como um Disco de Dados).

3. Em uma janela de Prompt de Comando, execute os seguintes comandos do AzCopy, substituindo <caminho_para_a_pasta_de_dados> pelo caminho para a pasta de dados criada em (2):

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxitrip /Dest:<path_to_data_folder> /S

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxifare /Dest:<path_to_data_folder> /S

	Quando o AzCopy for concluído, um total de 24 arquivos CSV zipados (12 de trip_data e 12 de trip_fare) devem estar na pasta de dados.

	>[AZURE.NOTE] *Fonte: https://getgoing.blob.core.windows.net/nyctaxitrip* e *Fonte: https://getgoing.blob.core.windows.net/nyctaxifare* especificam dois contêineres do Azure públicos que são usadas para compartilhar os dados descompactados de Táxi de NYC com os usuários.  Ler desses contêineres públicos do Azure não requer uma chave de acesso. 

## <a name="upload"></a>Carregar os dados para o contêiner padrão do cluster do Hadoop do Azure HDInsight

Em um Prompt de Comando ou uma janela do Windows PowerShell em sua máquina virtual, execute o seguinte comando AzCopy, substituindo os seguintes parâmetros pelos valores que você especificou ao criar o cluster do Hadoop.  Substitua:

* ***&#60;caminho_para_a_pasta_de_dados>*** com a pasta de dados que você criou na seção anterior 
* ***&#60; o nome da conta de armazenamento do cluster do Hadoop >*** pela conta de armazenamento usada pelo cluster
* ***&#60;contêiner padrão do cluster do Hadoop>*** pelo contêiner padrão usado pelo seu cluster
* ***&#60;chave de conta de armazenamento>*** pela chave para a conta de armazenamento usada pelo cluster

Observe que se ***nyctaxitripraw*** ou ***nyctaxifareraw*** referenciado neste comando não existir, eles serão criados no contêiner. 

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

## <a name="#hive-db-tables"></a>Criar banco de dados e tabelas Hive particionadas por mês

Para acessar e executar as consultas de Hive usadas neste passo a passo, faça logon no nó principal do cluster do Hadoop, abra a Linha de Comando do Hadoop na área de trabalho do nó principal e insira o diretório de Hive, digitando o comando

    cd %hive_home%\bin

Se precisar de qualquer assistência adicional com esses procedimentos ou procedimentos alternativos, consulte a seção [Enviar consultas de Hive diretamente da Linha de Comando do Hadoop ](machine-learning-data-science-process-hive-tables.md#submit). 

Se você criou uma máquina virtual do Azure seguindo as instruções [Configurar o Ambiente de Ciência de Dados do Azure](#setup), as consultas de Hive de exemplo já devem ter sido baixadas para sua máquina virtual na pasta **Scripts de Exemplo**.  Como alternativa, os usuários podem conferir os arquivos .hql no [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). 

Para enviar consultas de Hive em arquivos .hql, é necessário transferir os arquivos de .hql para um diretório local no nó principal do cluster do Hadoop.  Nos exemplos a seguir, vamos supor que os arquivos .hql foram transferidos para o diretório ***C:\temp &#92.*** no nó principal.

Aqui está o conteúdo do arquivo ***C:\temp\sample_hive_create_db_and_tables.hql*** que cria o banco de dados de Hive ***nyctaxidb*** e tabelas ***trip*** e ***fare***.

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	( 
	    medallion string, 
	    hack_license string,
	    vendor_id string, 
	    rate_code string, 
	    store_and_fwd_flag string, 
	    pickup_datetime string, 
	    dropoff_datetime string, 
	    passenger_count int, 
	    trip_time_in_secs double, 
	    trip_distance double, 
	    pickup_longitude double, 
	    pickup_latitude double, 
	    dropoff_longitude double, 
	    dropoff_latitude double)  
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare 
	( 
	    medallion string, 
	    hack_license string, 
	    vendor_id string, 
	    pickup_datetime string, 
	    payment_type string, 
	    fare_amount double, 
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Digite o seguinte comando na Linha de Comando do Hadoop do nó principal para enviar essas consultas de Hive:
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"


## <a name="#load-data"></a>Carregar dados para tabelas de partições
Execute os seguintes comandos do PowerShell na Linha de Comando do Hadoop para carregar dados para as tabelas Hive particionadas por mês:

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Aqui está o conteúdo do arquivo *sample_hive_load_data_by_partitions.hql* para inspeção.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/fare_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

### <a name="#show-db"></a>Mostrar os bancos de dados no cluster do Hadoop do HDInsight

Para mostrar os bancos de dados criados no cluster do Hadoop do HDInsight dentro da janela de Linha de Comando do Hadoop, execute o seguinte comando na Linha de Comando do Hadoop:

	hive -e "show databases;"

### <a name="#show-tables"></a>Mostrar as tabelas no banco de dados nyctaxidb 
	
Para mostrar as tabelas no banco de dados nyctaxidb, execute o seguinte comando na Linha de Comando do Hadoop:
	
	hive -e "show tables in nyctaxidb;"
   
## <a name="#explore-hive"></a>Exploração de dados e engenharia de recursos no Hive
As tarefas de exploração de dados e engenharia de recursos para os dados carregados nas tabelas Hive podem ser realizados usando consultas de Hive.  Aqui estão os exemplos de tais tarefas que percorreremos ao longo desta seção:

- Exibir os 10 primeiros registros em ambas as tabelas.
- Explorar as distribuições de dados de alguns campos em períodos diferentes.
- Investigar a qualidade dos dados dos campos de longitude e latitude.
- Gerar rótulos de classificação binária e multiclasse com base no **tip_amount**.
- Gerar recursos computando as distâncias diretas de viagem.
- Unir as duas tabelas e extrair uma amostra aleatória que será usada para compilar modelos.

Prever, depois de realizar essas explorações de dados e a engenharia de recursos no Hive, você estará pronto para prosseguir para o Aprendizado de Máquina do Azure.  Você pode salvar a consulta de Hive final para extrair os dados de exemplo e, em seguida, copiar e colar a consulta diretamente em um módulo de Leitor no Aprendizado de Máquina do Azure.  Assim, você estará pronto para compilar um modelo de dados.

### Exploração:  Exibir os 10 primeiros registros na tabela de viagens

Para compreender a natureza do esquema de dados e o como eles se parecem, os usuários podem extrair os 10 primeiros registros de cada tabela.  Execute as duas consultas seguintes separadamente no console de Linha de Comando do Hadoop para inspecionar os registros.

Para obter os 10 primeiros registros em *table _trip_*:

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
Para obter os 10 primeiros registros em *table _fare_*:
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

### Exploração:  Exibir o número de registros em cada uma das 12 partições

Execute o seguinte comando no console de Linha de Comando do Hadoop para exibir o número de registros em cada uma das 12 partições mensais.
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

### Exploração:  Distribuição de viagem por medalhão

Este exemplo identifica o medalhão (número do táxi) com mais de 100 viagens dentro de um determinado período de tempo.  A consulta aproveita o acesso à tabela particionada, já que ele está condicionada à variável de partição **mês**. 

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql"

Aqui está o conteúdo do arquivo *sample_hive_trip_count_by_medallion.hql* para inspeção.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

### Exploração:  Distribuição de viagem por medallion e hack_license

Aqui está um exemplo de agrupamento por várias colunas da tabela, nesse caso, as colunas medallion e hack_license.  Execute o seguinte comando no console de Linha de Comando do Hadoop:

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql"

Aqui está o conteúdo do arquivo *sample_hive_trip_count_by_medallion_license.hql* para inspeção.
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

### Avaliação de Qualidade de Dados:  Verificar registros de longitude e/ou latitude inválidos

Este exemplo investiga se qualquer um dos campos longitude e/ou latitude contém um valor inválido (graus radianos devem estar entre -90 e 90), ou tiver coordenadas (0, 0).

Execute o seguinte comando no console de Linha de Comando do Hadoop:

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

O argumento *-S* incluído neste comando suprime a impressão de tela de status dos trabalhos de Mapear/Reduzir de Hive.  Isso é útil, pois ele torna a tela de impressão da saída da consulta de Hive mais legível. 

Aqui está o conteúdo do arquivo *sample_hive_quality_assessment.hql* para inspeção.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'));

### Exploração:  Frequências de viagens com e sem gorjeta

Este exemplo localiza o número de viagens que receberam gorjetas em comparação com aquelas que não receberam em um determinado período de tempo (ou no conjunto de dados completo se abrangendo todo o ano).  Essa distribuição reflete a distribuição binária de rótulo a ser usada posteriormente para modelagem de classificação binária.

Execute o seguinte comando no console de Linha de Comando do Hadoop:

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

Aqui está o conteúdo do arquivo *sample_hive_tipped_frequencies.hql* para inspeção.

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

### Exploração:  Frequências de intervalos de gorjetas

Esse exemplo calcula a distribuição dos intervalos de gorjetas em um determinado período de tempo (ou no conjunto de dados completo se abrangendo todo o ano).  Essa é a distribuição das classes de rótulo que serão usados posteriormente para a modelagem de classificação multiclasse.

Execute o seguinte comando no console de Linha de Comando do Hadoop:

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

Aqui está o conteúdo de *sample_hive_tip_range_frequencies.hql* para inspeção.

	SELECT tip_class, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount=0, 0, 
            if(tip_amount>0 and tip_amount<=5, 1, 
            if(tip_amount>5 and tip_amount<=10, 2, 
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

### Exploração:  Computar a distância direta e comparar com a distância da viagem

Esse exemplo calcula a distância direta da viagem (em milhas).  O exemplo limita os resultados às coordenadas válidas apenas usando a consulta de avaliação de qualidade de dados abordada anteriormente.

Execute o seguinte comando no console de Linha de Comando do Hadoop:

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

Aqui está o conteúdo do arquivo *sample_hive_trip_direct_distance.hql* para inspeção.

    set R=3959;
    set pi=radians(180);
	
	insert overwrite directory 'wasb:///queryoutputdir'
    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
        pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
    from nyctaxidb.trip 
    where month=1 
        and pickup_longitude between -90 and -30
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and -30
        and dropoff_latitude between 30 and 90;

Depois que a consulta for concluída, os resultados são gravados em um blob do Azure ***queryoutputdir/000000_0*** no contêiner padrão do cluster do Hadoop.  Se você usar o Gerenciador de Armazenamento do Azure, você poderá ver esse blob no contêiner padrão do cluster do Hadoop, conforme mostrado na figura a seguir.

![Create workspace][2]

Observe que você pode aplicar o filtro (destacado pela caixa vermelha) para recuperar apenas o blob com letras especificadas em nomes. 

Depois que a consulta é concluída, você pode usar o SDK do Azure para ler a saída de consulta do blob do Azure em um quadro de dados pandas realizar mais explorações e processos.  Consulte [Processar dados de Blob do Azure em seu ambiente de ciência de dados](machine-learning-data-science-process-data-blob.md) para obter instruções sobre como ler blobs do Azure em quadros de dados pandas. 
	
### Preparar dados para criação de modelo

A consulta fornecida na seção une as tabelas **nyctaxidb.trip** e **nyctaxidb.fare**, gera um rótulo de classificação binária **tipped** e um rótulo de classificação de multiclasse **tip_class**.  Essa consulta pode ser copiada e colada diretamente no módulo Leitor do [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net) para ingestão de dados direta de **Consulta de Hive** no Azure.  Esta consulta também exclui os registros com as coordenadas de latitude e/ou longitude incorretas.

Essa consulta aplica UDFs com incorporação de Hive para gerar vários recursos na seção de registros, incluindo a hora, a semana do ano e dia da semana (1 significa segunda-feira e 7 representa domingo) do campo _pickup_datetime_.  Os usuários podem consultar [UDF LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) para ver uma lista completa das UDFs Hive incorporadas.

Esta consulta também reduz as amostras dos dados para que os resultados da consulta se adequem ao Estúdio de Aprendizado de Máquina do Azure.  Apenas cerca de 1% é importado para o Estúdio.

Envie a consulta executando o seguinte comando no console de Linha de Comando do Hadoop:

	hive -f "C:\temp\sample_hive_prepare_for_aml.hql"

Aqui está o conteúdo do arquivo *sample_hive_prepare_for_aml.hql* para inspeção.
	
    select 
        t.medallion, 
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        f.payment_type, 
        f.fare_amount, 
        f.surcharge, 
        f.mta_tax, 
        f.tip_amount, 
        f.tolls_amount, 
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
            if(tip_amount>0 and tip_amount<=5,1,
            if(tip_amount>5 and tip_amount<=10,2,
            if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
    from
    (
        select medallion, 
            hack_license,
            vendor_id,
            rate_code,
            store_and_fwd_flag,
            pickup_datetime,
            dropoff_datetime,
            passenger_count,
            trip_time_in_secs,
            trip_distance,
            pickup_longitude,
            pickup_latitude,
            dropoff_longitude,
            dropoff_latitude,
            rand() as sample_key 
        from nyctaxidb.trip
        where pickup_latitude between 30 and 60
            and pickup_longitude between -90 and -60
            and dropoff_latitude between 30 and 60
            and dropoff_longitude between -90 and -60
    )t
    join
    (
        select 
            medallion, 
            hack_license, 
            vendor_id, 
            pickup_datetime, 
            payment_type, 
            fare_amount, 
            surcharge, 
            mta_tax, 
            tip_amount, 
            tolls_amount, 
            total_amount
        from nyctaxidb.fare 
    )f
    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
    where t.sample_key<=0.01

## <a name="mlmodel"></a>Compilar modelos no Aprendizado de Máquina do Azure

Agora estamos prontos para prosseguir para a criação e implantação de modelo no [Aprendizado de Máquina do Azure](https://studio.azureml.net).  Os dados agora estão prontos para uso para resolver os problemas de previsão identificados acima:

1. **Classificação binária**:  Para prever se uma gorjeta foi ou não paga para uma viagem.

2. **Classificação multiclasse**:  Para prever o intervalo de valores de gorjetas pagas pela viagem, usando as classes definidas anteriormente. 

3. **Tarefa de regressão**:  Prever o valor da gorjeta paga por uma viagem.  

Para iniciar o exercício de modelagem, faça logon no seu espaço de trabalho do Aprendizado de Máquina do Azure.  Se você ainda não tiver criado uma espaço de trabalho de aprendizado de máquina, consulte [Criar um espaço de trabalho de AM do Azure](machine-learning-create-workspace.md).

1. Para ver os primeiros passos no Aprendizado de Máquina do Azure, consulte [O que é o Estúdio de Aprendizado de Máquina do Azure?](machine-learning-what-is-ml-studio.md)

2. Faça logon no [Estúdio de Aprendizado de Máquina do Azure](https://studio.azureml.net).

3. A página inicial do Estúdio fornece uma grande quantidade de informações, vídeos, tutoriais e links para a Referência de Módulos e outros recursos.  Para obter mais informações sobre o Aprendizado de Máquina do Azure, consulte o [Centro de Documentação do Aprendizado de Máquina do Azure](http://azure.microsoft.com/documentation/services/machine-learning/).

Uma experiência de treinamento típico consiste no seguinte:

1. Criar um **+NOVO** experimento.
2. Levar os dados ao AM do Azure.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gerar recursos conforme necessário.
5. Dividir os dados em conjuntos de dados de treinamento/validação/teste (ou conjuntos de dados separados para cada um desses).
6. Selecionar um ou mais algoritmos de aprendizado de máquina dependendo do problema de aprendizado a ser resolvido.  Por exemplo, classificação binária, classificação multiclasse ou regressão.
7. Treinar um ou mais modelos usando o conjunto de dados de treinamento.
8. Pontuar o conjunto de dados de validação usando os modelos treinados.
9. Avaliar os modelos para computar a métrica relevante para o problema de aprendizado.
10. Ajustar os modelos e selecionar o melhor modelo a ser implantado.

Neste exercício, já exploramos e engenhamos os dados no Hive (etapas 1 a 4), e escolhemos o tamanho da amostra para ingestão no AM do Azure.  Para compilar um ou mais dos modelos de previsão:

1. Obter os dados do AM do Azure usando o módulo do **Leitor** disponível na seção **Dados de entrada e saída**.  Para obter mais informações, consulte a página de referência de módulo do [Leitor](http://msdn.microsoft.com/library/dn784775).

	![Create workspace][15]

2. Selecione **Consulta Hive** como a **Fonte de Dados** no painel **Propriedades**.

3. Insira as informações de cluster do Hadoop do HDInsight da seguinte maneira.  O **nome da conta de armazenamento do Azure** deve ser a conta de armazenamento que é usada para criar o cluster do Hadoop do HDInsight e o **nome do contêiner do Azure** deve ser o contêiner padrão do cluster do Hadoop. 

	![Create workspace][11]

4. Na área de edição de texto **Consulta de banco de dados Hive**, cole a consulta que extrai os campos de banco de dados necessários (incluindo quaisquer campos calculados, como rótulos) e reduza as amostras de dados para o tamanho de amostra desejado.

Um exemplo de um experimento de classificação binária lendo dados diretamente da consulta de Hive é mostrado na figura abaixo.  Experimentos semelhantes podem ser construídos por meio de classificação multiclasse e problemas de regressão.

![Create workspace][12]

> [AZURE.IMPORTANT] Nos exemplos de modelagem de extração de dados e consulta de amostragem fornecidos nas seções anteriores, **todos os rótulos para os três exercícios de modelagem são incluídos na consulta**.  É uma etapa importante (obrigatória) em cada um dos exercícios modelagem **excluir** os rótulos desnecessários para os dois problemas e qualquer outro **vazamentos de destino**.  Por exemplo, ao usar classificação binária, use o rótulo **tipped** e exclua os campos **tip_class**, **tip_amount** e **total_amount**.  Esses últimos são vazamentos de destino, já que eles indicam a gorjeta paga.

> Nesse experimento, o primeiro módulo de **Editor de Metadados** adiciona nomes de coluna aos dados de saída do módulo Leitor.  Esse módulo é necessário porque o módulo de Leitor que lê dados Consulta de Hive não cria nomes de coluna para os dados de saída. 

> Para excluir as colunas desnecessárias e/ou vazamentos de destino, você pode usar o módulo **Colunas do Projeto** ou **Editor de Metadados**.  Para obter mais informações, consulte as páginas de referência [Colunas do Projeto](http://msdn.microsoft.com/library/dn784740) e [Editor de Metadados](http://msdn.microsoft.com/library/dn784761).

## <a name="mldeploy"></a>Implantar modelos no Aprendizado de Máquina do Azure

Quando o modelo estiver pronto, você pode implantá-lo como um serviço Web diretamente do experimento.  Para obter mais informações sobre a publicação de serviços Web de AM do Azure, consulte [Operações de serviço de API de Aprendizado de Máquina do Azure](../machine-learning-overview-of-azure-ml-process.md).

Para implantar um novo serviço Web, você precisa:

1. Criar um experimento de pontuação.
2. Publicar o serviço Web.

Para criar um experimento de pontuação por meio de um experimento de treinamento **Concluído**, clique em **CRIAR EXPERIMENTO DE PONTUAÇÃO** na barra de ação inferior.

![Azure Scoring][13]

O Aprendizado de Máquina do Azure tentará criar um experimento de pontuação com base nos componentes do experimento de treinamento.  Em especial, ele vai:

1. Salvar o modelo treinado e remover os módulos de treinamento de modelo.
2. Identificar uma **porta de entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identificar uma **porta de saída** lógica para representar o esquema de saída do serviço Web.

Quando o experimento de pontuação é criado, analisá-lo e ajustar conforme necessário.  Um ajuste típico é substituir o conjunto de dados de entrada e/ou a consulta por uma exclua os campos de rótulo, pois eles não estarão disponíveis quando o serviço for chamado.  Também é uma prática recomendada reduzir o tamanho do conjunto de dados de entrada e/ou da consulta a apenas alguns registros, suficientes para indicar o esquema de entrada.  Para a porta de saída, é comum para excluir todos os campos de entrada para incluir apenas o **Rótulos Pontuados** e **Possibilidades Pontuadas** na saída usando o módulo **Colunas de Projeto**.

Um exemplo de experimento de pontuação é mostrado na figura abaixo.  Quando estiver pronto para publicar, clique no botão **PUBLICAR SERVIÇO WEB** na barra de ação inferior.

![Create workspace][14]

Para recapitular, neste tutorial passo a passo você criou um ambiente de ciência de dados do Azure que funciona com um grande conjunto de dados públicos.  Começando na aquisição de dados e prosseguindo pelas tarefas de exploração e engenharia de recursos no processo de ciência de dados, até o treinamento e publicação de modelo de um serviço Web de Aprendizado de Máquina do Azure.

## Informações de Licença

Este passo a passo do exemplo e os scripts que o acompanham são compartilhados pela Microsoft sob a licença MIT.  Verifique o arquivo LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

## Referências

*	[Página de download de Viagens de Táxi de NYC de Andrés Monroy](http://www.andresmh.com/nyctaxitrips/)  
*	[FOILing em dados de Viagem de Táxi de NYC por Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[Pesquisa e estatísticas de comissionamento de táxis e limusines de NYC](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!--HONumber=49--> 