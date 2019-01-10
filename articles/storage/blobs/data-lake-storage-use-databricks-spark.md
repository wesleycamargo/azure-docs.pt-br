---
title: 'Tutorial: Acessar dados da versão prévia do Azure Data Lake Storage Gen2 com Azure Databricks usando o Spark | Microsoft Docs'
description: Este tutorial mostra como executar consultas Spark em um cluster do Azure Databricks para acessar dados em uma conta de armazenamento do Azure Data Lake Storage Gen2.
services: storage
author: dineshmurthy
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: dineshm
ms.openlocfilehash: b0382d31f9d16228ca3447ace9c7d4f171b206f6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548979"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Tutorial: Acessar dados da Versão Prévia do Data Lake Storage Gen2 com o Azure Databricks usando o Spark

Este tutorial mostra como conectar seu cluster do Azure Databricks aos dados armazenados em uma conta de Armazenamento do Azure que tenha o Azure Data Lake Storage Gen2 (Versão Prévia) habilitado. Essa conexão permite que você execute nativamente consultas e análises nos dados por meio do cluster.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Criar um cluster Databricks
> * Ingerir dados não estruturados em uma conta de armazenamento
> * Executar a análise em seus dados no armazenamento de blobs

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial demonstra como consumir e consultar dados de um voo aéreo, disponibilizados pela [Agência Nacional de Aviação dos EUA (United States Department of Transportation)](https://transtats.bts.gov/DL_SelectFields.asp). 

1. Marque a caixa de seleção **Arquivo pré-compactado** para selecionar todos os campos de dados.
2. Selecione **Baixar** e salve os resultados no computador.
3. Anote o nome de arquivo e o caminho do download; você precisará dessas informações em uma etapa posterior.

Para concluir este tutorial, você precisará de uma conta de armazenamento com funcionalidades analíticas. Recomendamos a conclusão de nosso [início rápido](data-lake-storage-quickstart-create-account.md) sobre o assunto para criar uma. Depois de criá-la, navegue para a conta de armazenamento para recuperar as definições de configuração.

1. Em **Configurações**, selecione **Chaves de acesso**.
2. Selecione o botão **Copiar** ao lado de **key1** para copiar o valor da chave.

O nome da conta e a chave são necessários para etapas posteriores neste tutorial. Abra um editor de texto e coloque o nome da conta e a chave para referência futura.

## <a name="create-a-databricks-cluster"></a>Criar um cluster Databricks

A próxima etapa é criar um cluster do Databricks para criar um workspace de dados.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso**.
2. Insira **Azure Databricks** no campo de pesquisa.
3. Selecione **Criar** na folha do Azure Databricks.
4. Nomeie o serviço Databricks **myFlightDataService** (marque a caixa de seleção *Fixar no painel* quando criar o serviço).
5. Selecione **Iniciar Workspace** para abrir o workspace em uma nova janela do navegador.
6. Selecione **Clusters** na barra de navegação à esquerda.
7. Selecione **Criar Cluster**.
8. Insira um **myFlightDataCluster** no campo **Nome do cluster**.
9. Selecione **Standard_D8s_v3** no campo **Tipo de Trabalho**.
10. Altere o valor **Mín. de trabalhos** para **4**.
11. Selecione **Criar Cluster** na parte superior da página. (Este processo poderá levar até 5 minutos para ser concluído.)
12. Quando o processo for concluído, selecione **Azure Databricks** no canto superior esquerdo da barra de navegação.
13. Selecione **Notebook** na seção **Novo** na metade inferior da página.
14. Insira um nome de sua escolha no campo **Nome** e selecione **Python** como a linguagem.
15. Todos os outros campos podem ser deixados com valores padrão.
16. Selecione **Criar**.
17. Cole o código a seguir na célula **Cmd 1**. Substitua os espaços reservados mostrados entre colchetes na amostra pelos seus próprios valores:

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
        
    dbutils.fs.mount(
        source = "abfss://dbricks@<account-name>.dfs.core.windows.net/folder1",
        mount_point = "/mnt/flightdata",
        extra_configs = configs)
    ```
18. Pressione **SHIFT + ENTER** para executar a célula de código.

## <a name="ingest-data"></a>Ingestão de dados

### <a name="copy-source-data-into-the-storage-account"></a>Copiar dados de origem para a conta de armazenamento

A próxima tarefa é usar o AzCopy para copiar dados do arquivo *.csv* para o armazenamento do Azure. Abra uma janela do prompt de comando e digite os comandos a seguir. Substitua os espaços reservados `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` e `<ACCOUNT_KEY>` pelos valores correspondentes que você reservou em uma etapa anterior.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Use o Databricks Notebook para converter CSV em Parquet

Abra o Databricks novamente no navegador e siga as seguintes etapas:

1. Selecione **Azure Databricks** no canto superior esquerdo da barra de navegação.
2. Selecione **Notebook** na seção **Novo** na metade inferior da página.
3. Digite **CSV2Parquet** no campo **Nome**.
4. Todos os outros campos podem ser deixados com valores padrão.
5. Selecione **Criar**.
6. Cole o código a seguir na célula **Cmd 1**. (Este código é salvo automaticamente no editor.)

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>Explorar dados

Retorne ao workspace do Databricks e selecione o ícone **Recentes** na barra de navegação à esquerda.

1. Selecione o notebook **Análise de Dados de Voo**.
2. Pressione **Ctrl+Alt+N** para criar uma nova célula.

Insira cada um dos blocos de código a seguir em **Cmd 1** e pressione **Cmd+Enter** para executar o script de Python.

Para obter uma lista de arquivos CSV carregados por meio do AzCopy, execute o seguinte script:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

Para criar um novo arquivo e listá-los na pasta *parquet/voos*, execute este script:

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

Com esses exemplos de código, você explorou a natureza hierárquica do HDFS usando dados armazenados em uma conta de armazenamento com o Data Lake Storage Gen2 habilitado.

## <a name="query-the-data"></a>Consultar os dados

Em seguida, você pode começar a consultar os dados carregados na sua conta de armazenamento. Insira cada um dos blocos de código a seguir em **Cmd 1** e pressione **Cmd+Enter** para executar o script de Python.

### <a name="run-simple-queries"></a>Executar consultas simples

Para criar dataframes para suas fontes de dados, execute o seguinte script:

> [!IMPORTANT]
> Substitua o espaço reservado **<NOME_DO_SEU_ARQUIVO_CSV>** pelo nome do arquivo baixado no início deste tutorial.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

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
#preferably run this in a separate cmd cell
display(flightDF)
```

Para executar consultas de análise nos dados, execute o seguinte script:

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
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

### <a name="run-complex-queries"></a>Executar consultas complexas

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

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos da conta de armazenamento e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

[!div class="nextstepaction"] 
> [Extrair, transformar e carregar dados usando o Apache Hive no HDInsight do Azure](data-lake-storage-tutorial-extract-transform-load-hive.md)

