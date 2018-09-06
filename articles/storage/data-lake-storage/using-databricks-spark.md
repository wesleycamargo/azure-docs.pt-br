---
title: Acessar dados da versão prévia do Azure Data Lake Storage Gen2 com Azure Databricks usando o Spark | Microsoft Docs
description: Saiba como executar consultas Spark em um cluster Azure Databricks para acessar dados em uma conta de Azure Data Lake Storage Gen2.
services: hdinsight,storage
author: dineshm
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 7d951a959da28187a5971ee218f2bd921d331727
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301791"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Tutorial: Acessar dados da versão prévia do Azure Data Lake Storage Gen2 com Azure Databricks usando o Spark

Neste tutorial, você aprenderá a executar consultas Spark em um cluster Azure Databricks para consultar dados na versão prévia da Conta de Azure Data Lake Storage Gen2.

> [!div class="checklist"]
> * Criar um cluster Databricks
> * Ingerir dados não estruturados em uma conta de armazenamento
> * Disparar uma função do Azure para processar dados
> * Executar a análise em seus dados no armazenamento de blobs

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como consumir e consultar dados de um voo aéreo, disponibilizados pela [Agência Nacional de Aviação dos EUA (United States Department of Transportation)](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time). Baixe pelo menos dois anos de dados de linhas aéreas (selecionando todos os campos) e salve o resultado em seu computador. Anote o nome de arquivo e o caminho do download; você precisará dessas informações em uma etapa posterior.

> [!NOTE]
> Marque a caixa de seleção **Arquivo pré-compactado** para selecionar todos os campos de dados. O download terá muitos gigabytes, mas essa quantidade de dados é necessária para a análise.

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>Criar uma conta de Armazenamento do Azure Data Lake Gen2

Para começar, crie uma nova [conta de Armazenamento do Azure Data Lake Gen2](quickstart-create-account.md) e dê a ela um nome exclusivo. Navegue para a conta de armazenamento a fim de recuperar as definições de configuração.

> [!IMPORTANT]
> Durante a versão prévia, o Azure Functions funciona somente com contas de Armazenamento do Azure Data Lake Gen2 criadas com um namespace simples.

1. Em **Configurações**, clique em **Chaves de acesso**.
3. Clique em **Copiar** ao lado de **key1** para copiar o valor da chave.

O nome da conta e a chave são necessários para etapas posteriores neste tutorial. Abra um editor de texto e coloque o nome da conta e a chave para referência futura.

## <a name="create-a-databricks-cluster"></a>Criar um cluster Databricks

A próxima etapa é criar um [cluster Databricks](https://docs.azuredatabricks.net/) para criar um espaço de trabalho de dados.

1. Crie um [serviço Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) e nomeie-o **myFlightDataService** (marque a caixa de seleção *Fixar no painel* quando criar o serviço).
2. Clique em **Iniciar Espaço de Trabalho** para abrir o espaço de trabalho em uma nova janela do navegador.
3. Clique em **Clusters** na barra de navegação esquerda.
4. Clique em **Criar Cluster**.
5. Insira um **myFlightDataCluster** no campo *Nome do cluster*.
6. Selecione **Standard_D8s_v3** no campo *Tipo de Trabalho*.
7. Altere o valor **Mín. de trabalhos** para *4*.
8. Clique em **Criar Cluster** na parte superior da página (esse processo pode levar até 5 minutos para terminar).
9. Quando o processo for concluído, selecione **Azure Databricks** na parte superior esquerda da barra de navegação.
10. Selecione **Notebook** na seção **Novo** na metade inferior da página.
11. Insira um nome de sua escolha no campo **Nome** e selecione **Python** como linguagem.
12. Todos os outros campos podem ser deixados com valores padrão.
13. Selecione **Criar**.
14. Cole o código a seguir na célula **Cmd 1** e substitua os valores pelos valores provenientes da conta de armazenamento.

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfss://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>Ingestão de dados

### <a name="copy-source-data-into-the-storage-account"></a>Copiar dados de origem para a conta de armazenamento

A próxima tarefa é usar o AzCopy para copiar dados do arquivo *.csv* para o armazenamento do Azure. Abra uma janela do prompt de comando e digite os comandos a seguir. Substitua os espaços reservados `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` e `<ACCOUNT_KEY>` pelos valores correspondentes que você reservou em uma etapa anterior.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Use o Databricks Notebook para converter CSV em Parquet

Abra novamente o Databricks no navegador e execute as seguintes etapas:

1. Selecione **Azure Databricks** na parte superior esquerda da barra de navegação.
2. Selecione **Notebook** na seção **Novo** na metade inferior da página.
3. Digite **CSV2Parquet** no campo **Nome**.
4. Todos os outros campos podem ser deixados com valores padrão.
5. Selecione **Criar**.
6. Cole o código a seguir na célula **1 Cmd** (esse código é salvo automaticamente no editor).

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>"
    accountkey = " <insert account key>"
    fullname = "fs.azure.account.key." +accountname+ ".dfs.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".dfs.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Explorar dados usando o Sistema de Arquivos Distribuído Hadoop

Retorne ao espaço de trabalho do Databricks e clique no ícone **Recentes** na barra de navegação à esquerda.

1. Clique no notebook **Análise de Dados de Voo**.
2. Pressione **Ctrl+Alt+N** para criar uma nova célula.

Insira cada um dos blocos de código a seguir em **Cmd 1** e pressione **Cmd+Enter** para executar o script de Python.

Para obter uma lista de arquivos CSV carregados por meio do AzCopy, execute o seguinte script:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

Para criar um novo arquivo e listá-los na pasta *parquet/voos*, execute este script:

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
Com esses exemplos de código, você explorou a natureza hierárquica do HDFS usando dados armazenados em uma conta de Armazenamento do Azure Data Lake Gen2 funcional.

## <a name="query-the-data"></a>Consultar os dados

Em seguida, você pode começar a consultar os dados carregados no Armazenamento do Azure Data Lake. Insira cada um dos blocos de código a seguir em **Cmd 1** e pressione **Cmd+Enter** para executar o script de Python.

### <a name="simple-queries"></a>Consultas simples

Para criar dataframes para suas fontes de dados, execute o seguinte script:

> [!IMPORTANT]
> Substitua o espaço reservado **<NOME_DO_SEU_ARQUIVO_CSV>** pelo nome do arquivo baixado no início deste tutorial.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferrably run this in a separate cmd cell
display(flightDF)
```

Para executar consultas de análise nos dados, execute o seguinte script:

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```
### <a name="complex-queries"></a>Consultas complexas

Para executar os consultas mais complexas a seguir, execute um segmento de cada vez no notebook e inspecione os resultados.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="next-steps"></a>Próximas etapas

* [Extrair, transformar e carregar dados usando o Apache Hive no HDInsight do Azure](tutorial-extract-transform-load-hive.md)
