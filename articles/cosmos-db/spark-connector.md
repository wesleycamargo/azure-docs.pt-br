---
title: Conectando o Apache Spark ao Azure Cosmos DB | Microsoft Docs
description: Use este tutorial para saber mais sobre o conector do Spark ao Azure Cosmos DB que permite a conexão do Apache Spark ao Azure Cosmos DB para realizar agregações distribuídas e ciências de dados no banco de dados multilocatário distribuído globalmente da Microsoft projetado para a nuvem.
keywords: apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: cae66a40882231f7762af29cdeaaf658dd2aa968
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113940"
---
# <a name="accelerate-real-time-big-data-analytics-by-using-the-spark-to-azure-cosmos-db-connector"></a>Acelerar a análise de Big Data em tempo real usando o conector do Spark para Azure Cosmos DB
 
O conector do Spark para Azure Cosmos DB permite que o Azure Cosmos DB atue como uma entrada ou saída para trabalhos do Apache Spark. Conectar o [Spark](http://spark.apache.org/) ao [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) acelera sua capacidade de solucionar problemas de ciência de dados ágeis na qual você pode usar o Azure Cosmos DB para manter e consultar dados rapidamente. O conector do Spark ao Azure Cosmos DB utiliza com eficiência os índices gerenciados nativos do Azure Cosmos DB. Os índices habilitam colunas atualizáveis quando você executa uma análise e filtragem predicada em dados de alteração rápida distribuídos globalmente, que variam de Internet das coisas (IoT) para ciência de dados e cenários de análise.

Saiba mais sobre o conector do Spark para Azure Cosmos DB neste vídeo:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>Componentes do conector

O conector do Spark para Azure Cosmos DB utiliza os componentes a seguir:

* O [Azure Cosmos DB](http://documentdb.com) permite que os clientes provisionem e dimensionem de forma elástica a produtividade e o armazenamento em qualquer quantidade de regiões geográficas.  

* O [Apache Spark](http://spark.apache.org/) é um mecanismo de processamento eficiente com código-fonte aberto criado pensando em velocidade, facilidade de uso e análise sofisticada.  

* [Cluster do Apache Spark no Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) para executar trabalhos do Spark no cluster do Spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Conectar Apache Spark ao Azure Cosmos DB

Há duas abordagens para conectar o Apache Spark e o Azure Cosmos DB:

1. Usando [SDK do Python de SQL do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-python), um Spark baseado em Python para conector do Cosmos DB, que também é referido como “pyDocumentDB”.  

2. Usando o [SDK do Java de SQL do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java) um Spark baseado em Java para conector do Cosmos DB.


**Versões com suporte**

| Componente | Versão |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Versão de tempo de execução do Databricks | > 3.4 |
| SDK de SQL Java do Azure Cosmos DB | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Conectar usando SDK do Python ou pyDocumentDB

A imagem a seguir mostra a arquitetura da implementação do SDK pyDocumentDB:

![Fluxo de dados do Spark ao Azure Cosmos DB por meio do banco de dados do pyDocumentDB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Fluxo de dados

O fluxo de dados da implementação do pyDocumentDB é conforme a seguir:

* O nó mestre do Spark conecta o nó de gateway do Azure Cosmos DB por meio do pyDocumentDB. Um usuário especifica apenas as conexões do Spark e Azure Cosmos DB. Conexões com os respectivos nós mestre e gateway são transparentes para o usuário.  

* O nó de gateway executa a consulta no Azure Cosmos DB, no qual a consulta é executada posteriormente em partições da coleção nos nós de dados. A resposta para essas consultas é enviada de volta para o nó de gateway e esse conjunto de resultados é retornado para o nó mestre do Spark.  

* Consultas subsequentes (por exemplo, em uma estrutura de dados do Spark) são enviadas aos nós de trabalho do Spark para processamento.  

A comunicação entre o Spark e o Azure Cosmos DB é limitada ao nó mestre do Spark e aos nós de gateway do Azure Cosmos DB. As consultas avança tão depressa como a camada de transporte entre esses dois nós permite.

Execute as etapas a seguir para conectar o Spark ao Azure Cosmos DB usando o SDK pyDocumentDB:

1. Crie um [espaço de trabalho do Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) e um [cluster do Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Versão de tempo de execução do Databricks 4.0 (inclui o Apache Spark 2.3.0, Scala 2.11) dentro desse espaço de trabalho.  

2. Depois que o cluster for criado e estiver em execução, navegue até **Espaço de Trabalho** > **Criar** > **Biblioteca**.  
3. Na caixa de diálogo Nova Biblioteca, escolha **Carregar Python Egg ou PyPi** como a fonte, forneça **pydocumentdb** como o nome e selecione **Instalar Biblioteca**. O SDK PyDocumentdb já é publicado nos pacotes de pip para que seja possível localizá-lo e instalá-lo. 

   ![Criar e anexar biblioteca](./media/spark-connector/create-library.png)

4. Depois que a biblioteca estiver instalada, conecte-a ao cluster que você criou anteriormente.  

5. Em seguida, navegue até o **Espaço de Trabalho** > **Criar** > **Notebook**.  

6. Na caixa de diálogo **Criar Notebook**, insira um nome amigável, escolha **Python** como a linguagem. Na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

7. A simplicidade do transporte de comunicação torna a execução de uma consulta de Spark no Azure Cosmos DB usando o pyDocumentDB relativamente simples. Em seguida, você executará algumas consultas do Spark, usando os dados de exemplo de voos hospedados na conta "doctorwho" do Cosmos DB que é acessível publicamente. A versão HTML do notebook é hospedada no repositório GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Você deve baixar os arquivos do repositório e navegar até `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html` para importar o notebook para a conta do Azure Databricks e executá-lo. A seção a seguir explica a funcionalidade dos blocos de código em detalhes.

O trecho de código a seguir mostra como importar o SDK pyDocumentDB e executar uma consulta no contexto do Spark. Conforme observado no trecho de código, o SDK pyDocumentDB contém os parâmetros de conexão necessários para conectar a conta do Azure Cosmos DB. Ele importa as bibliotecas necessárias, configura a chave mestra e o host para criar o cliente do Azure Cosmos DB (pydocumentdb.document_client).


```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]

# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)

```

Em seguida, será possível executar consultas, o trecho de código a seguir conecta a coleção airports.codes na conta DoctorWho e executa uma consulta para extrair as cidades do aeroporto no estado de Washington. 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

Depois que a consulta for executada, o resultado será um “query_iterable.QueryIterable” que será convertido em uma lista do Python e que, em seguida, será convertido em uma estrutura de dados do Spark. 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

## <a name="considerations-when-using-pydocumentdb-sdk"></a>Considerações ao usar o SDK pyDocumentDB
Conectar o Spark ao Azure Cosmos DB, utilizando o SDK pyDocumentDB é recomendável nos cenários a seguir:

* Você deseja usar Python.  

* Você está retornando um conjunto de resultados relativamente pequeno do Azure Cosmos DB para o Spark. Observe que o conjunto de dados subjacente no Azure Cosmos DB pode ser muito grande e você está aplicando filtros ou executando filtros de predicado na fonte do Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Conectar usando o SDK do Java

A imagem a seguir mostra a arquitetura da implementação do SDK do Java de SQL do Azure Cosmos DB e as movimentações de dados entre os nós de trabalho do Spark:

![Fluxo de dados no conector do Spark ao Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Fluxo de dados

O fluxo de dados da implementação do SDK do Java é conforme a seguir:

* O nó mestre do Spark conecta com o nó de gateway do Azure Cosmos DB para obter o mapa de partições. Um usuário especifica apenas as conexões do Spark e do Azure Cosmos DB. Conexões com os respectivos nós mestre e gateway são transparentes para o usuário.  

* Essas informações são fornecidas de volta ao nó mestre do Spark. Neste ponto, você deve conseguir analisar a consulta para determinar as partições e suas localizações no Azure Cosmos DB que você precisa acessar.  

* Essas informações são transmitidas para os nós de trabalho do Spark.  

* Os nós de trabalho do Spark conectam diretamente as partições do Azure Cosmos DB para extrair os dados e retornar os dados para as partições do Spark nos nós de trabalho do Spark.  

A comunicação entre o Spark e o Azure Cosmos DB é consideravelmente mais rápida, pois a movimentação de dados ocorre entre os nós de trabalho do Spark e os nós de dados do Azure Cosmos DB (partições). Neste documento, você enviará alguns exemplos de dados do Twitter para a conta do Azure Cosmos DB e executará consultas do Spark usando esses dados. Use as etapas a seguir para gravar dados do Twitter de exemplo no Azure Cosmos DB:

1. Crie um [conta de API de SQL do Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) e [adicione uma coleção](create-sql-api-dotnet.md#add-a-collection) à conta.  

2. Baixe o exemplo [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) do GitHub, que é usado para gravar dados do Twitter de exemplo no Azure Cosmos DB.  

3. Abra um prompt de comando e instale os módulos Tweepy e pyDocumentdb, executando os comandos a seguir:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Extraia o conteúdo da exemplo de feed do Twitter e abra o arquivo config.py. Atualize os valores de masterKey, host, databaseId, collectionId e preferredLocations.  

5. Navegue até `http://apps.twitter.com/` e registre o script de feed do Twitter como um novo aplicativo. Após escolher um nome e um aplicativo para o aplicativo, você receberá uma **chave do consumidor, segredo do consumidor, token de acesso e segredo do token de acesso**. Copie esses valores e atualize-os no arquivo config.py para fornecer o acesso programático do aplicativo ao Twitter.   

6. Salve o arquivo config.py. Abra o prompt de comando e execute o aplicativo python usando o seguinte comando:

   ```bash
   Python driver.py
   ```

7. Navegue até a coleção do Azure Cosmos DB no portal e verifique se os dados do Twitter são gravados na coleção.

### <a name="find-and-attach-java-sdk-to-the-spark-cluster"></a>Localize e anexe o SDK do Java ao cluster do Spark

1. Crie um [espaço de trabalho do Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) e um [cluster do Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Versão de tempo de execução do Databricks 4.0 (inclui o Apache Spark 2.3.0, Scala 2.11) dentro desse espaço de trabalho.  

2. Depois que o cluster for criado e estiver em execução, navegue até **Espaço de Trabalho** > **Criar** > **Biblioteca**.  

3. Na caixa de diálogo Nova Biblioteca, escolha **Coordenada do Maven** como a fonte, forneça o valor da coordenada **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0** e selecione **Criar Biblioteca**. As dependências do Maven são resolvidas e o pacote é adicionado ao espaço de trabalho. No formato de coordenadas do Maven acima, 2.3.0 representa a versão do Spark, 2.11 representa a versão do Scala e 1.2.0 representa a versão do conector do Azure Cosmos DB. 

4. Depois que a biblioteca estiver instalada, conecte-a ao cluster que você criou anteriormente. 

Este artigo demonstra o uso do SDK do Java do conector do Spark nos cenários a seguir:

* Ler dados do Twitter a partir do Azure Cosmos DB  

* Ler dados do Twitter que estão sendo transmitidos para o Azure Cosmos DB  

* Gravar dados do twitter para Azure Cosmos DB 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Ler dados do Twitter a partir do Azure Cosmos DB
 
Nesta seção, você executará consultas do Spark para ler um lote de dados do Twitter a partir do Azure Cosmos DB. A versão HTML do notebook é hospedada no repositório GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). É necessário baixar os arquivos do repositório e navegar até `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html` para importar o notebook para a conta do Azure Databricks, atualizar o URI da conta, a chave mestra, o banco de dados, os nomes das coleções e executá-lo ou criar o notebook, conforme a seguir:

1. Navegue até a conta do Azure Databricks e selecione a **Espaço de Trabalho** > **Criar** > **Notebook**. 

2. Na caixa de diálogo **Criar Notebook**, insira um nome amigável, escolha **Python** como a linguagem, na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

3. Atualize os valores do ponto de extremidade, chave mestra, banco de dados e coleção para conectar a conta e ler os tweets, usando o comando spark.read.format().

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. Execute a consulta para obter a contagem de tweets por hashtags diferentes dos dados armazenados em cache. 

   ```python
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

O SDK do Java dá suporte aos valores a seguir para mapeamento de configuração: 

|Configuração  |DESCRIÇÃO  |
|---------|---------|
|query_maxdegreeofparallelism  | Define o número de operações simultâneas executadas no lado do cliente durante a execução da consulta paralela. Se estiver definido para um valor maior que 0, limita o número de operações simultâneas ao valor atribuído. Se ele for definido como menor que 0, o sistema decide automaticamente o número de operações simultâneas para executar. Como o Connector mapeia cada partição de coleção com um executor, esse valor não terá nenhum efeito na operação de leitura.        |
|query_maxbuffereditemcount     |    Define o número máximo de itens que podem ser armazenados em buffer no lado do cliente durante a execução da consulta paralela. Se estiver definido para um valor maior que 0, ele limitará o número de itens em buffer ao valor atribuído. Se estiver definido para menos de 0, o sistema decide automaticamente o número de itens a serem armazenados em buffer.     |
|query_enablescan    |   Define a opção para habilitar os exames nas consultas que não puderam ser exibidas porque a indexação foi recusada nos caminhos solicitados.       |
|query_disableruperminuteusage  |  Desabilita a capacidade das RUs (Unidades de Solicitação)/minuto para atender à consulta, se as RUs/segundo provisionadas regularmente estiverem esgotadas.       |
|query_emitverbosetraces   |   Define a opção para permitir que as consultas emitam rastreios detalhados para investigação.      |
|query_pagesize  |   Define o tamanho da página de resultados da consulta para cada solicitação de consulta. Para otimizar a taxa de transferência, use um tamanho de página grande para reduzir o número de viagens de ida e volta para buscar resultados de consultas.      |
|query_custom  |  Define a consulta do Azure Cosmos DB para substituir a consulta padrão ao buscar dados do Azure Cosmos DB. Observe que, quando esse valor for fornecido, ele também será usado no lugar de uma consulta com predicados propagados.     |

Dependendo do cenário, diferentes valores de configuração devem ser usados para otimizar o desempenho e a taxa de transferência. Observe que a chave de configuração não faz distinção entre maiúsculas e minúsculas e o valor de configuração é sempre uma cadeia de caracteres.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Ler dados do Twitter que estão sendo transmitidos para o Azure Cosmos DB

Nesta seção, você executará consultas do Spark para ler um feed de alterações do streaming de dados do Twitter. Enquanto você executa as consultas nesta seção, verifique se o aplicativo de feed do Twitter está em execução e enviando dados para o Azure Cosmos DB. A versão HTML do notebook é hospedada no repositório GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). É necessário baixar os arquivos do repositório e navegar até `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html` para importar o notebook para a conta do Azure Databricks, atualizar o URI da conta, a chave mestra, o banco de dados, os nomes das coleções e executá-lo ou criar o notebook, conforme a seguir:

1. Navegue até a conta do Azure Databricks e selecione a **Espaço de Trabalho** > **Criar** > **Notebook**.  

2. Na caixa de diálogo **Criar Notebook**, insira um nome amigável, escolha **Scala** como a linguagem, na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

3. Atualize os valores do ponto de extremidade, chave mestra, banco de dados e coleção para conectar a conta.

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. Inicie a leitura do feed de alterações como um fluxo, usando o comando spark.readStream.format():

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Inicie a consulta de streaming no console:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

O SDK do Java dá suporte aos valores a seguir para mapeamento de configuração:

|Configuração  |DESCRIÇÃO  |
|---------|---------|
|readchangefeed   |  Indica que o conteúdo da coleção é buscado no Feed de Alterações do CosmosDB. O valor padrão é falso.       |
|changefeedqueryname |   Uma cadeia de caracteres personalizada para identificar a consulta. O conector controla os tokens de continuação de coleção para diferentes consultas de alterações de feed separadamente. Se readchangefeedis for verdadeiro, essa é uma configuração necessária que não poderá ter valor vazio.      |
|changefeedcheckpointlocation  |   Um caminho para o armazenamento de arquivos local para persistir tokens de continuação no caso de falhas de nó.      |
|changefeedstartfromthebeginning  |  Define se o feed de alterações deve iniciar do começo (verdadeiro) ou do ponto atual (falso). Por padrão, ele inicia a partir do atual (falso).       |
|rollingchangefeed  |   Um valor booliano que indica se o feed de alterações deve ser da última consulta. O valor padrão é falso, o que significa que as alterações serão contadas a partir da primeira leitura da coleção.      |
|changefeedusenexttoken  |   Um valor booliano para dar suporte a cenários de falha de processamento. Ele é usado para indicar que o lote de feed de alterações atual foi tratado normalmente e o RDD deve usar os próximos tokens de continuação para obter o lote subsequente de alterações.      |
| InferStreamSchema | Um valor booliano que indica se o esquema dos dados de streaming deve ser amostrado no início do streaming. Por padrão, esse valor é definido como verdadeiro. Se esse parâmetro for definido como verdadeiro e o esquema dos dados de streaming for alterado depois que os dados forem amostrados, as propriedades adicionadas recentemente serão removidas na estrutura de dados de streaming. <br/><br/> Se você quiser que a estrutura de dados de streaming seja independente de esquema, defina esse parâmetro para falso. Nesse modo, o corpo dos documentos lidos do feed de alterações do Azure Cosmos DB é encapsulado em uma propriedade ‘body’" na estrutura de dados de streaming resultante, além dos valores da propriedade do sistema.
 |

### <a name="connection-settings"></a>Configurações de conexão

O SDK do Java dá suporte às configurações de conexão a seguir:

|Configuração  |DESCRIÇÃO  |
|---------|---------|
|connectionmode   |  Define o modo de conexão que o DocumentClient interno deve usar para comunicar-se com o Azure Cosmos DB. Os valores permitidos são **DirectHttps** (valor padrão) e **Gateway**. O modo de conexão DirectHttps encaminha as solicitações diretamente para as partições do Cosmos DB e fornece alguma vantagem de latência.       |
|connectionmaxpoolsize   |  Define o valor do tamanho do pool de conexões usado pelo DocumentClient interno. O valor padrão é 100.       |
|connectionidletimeout  |  Define o valor de tempo limite para conexões ociosas em segundos. O valor padrão é 60.       |
|query_maxretryattemptsonthrottledrequests    |  Define o número máximo de repetições. Esse valor é usado no caso de uma falha de solicitação devido à limitação da taxa no cliente. Se não for especificado, o valor padrão será 1000 tentativas de repetição.       |
|query_maxretrywaittimeinseconds   |  Define o tempo máximo de repetição em segundos. Por padrão, é de 1000 segundos.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Gravar dados do twitter para Azure Cosmos DB 

Nesta seção, você executará consultas do Spark para gravar um lote de dados do Twitter em uma nova coleção no mesmo banco de dados. A versão HTML do notebook é hospedada no repositório GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). É necessário baixar os arquivos do repositório e navegar até `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html` para importar o notebook para a conta do Azure Databricks, atualizar o URI da conta, a chave mestra, o banco de dados, os nomes das coleções e executá-lo ou criar o notebook, conforme a seguir:

1. Navegue até a conta do Azure Databricks e selecione a **Espaço de Trabalho** > **Criar** > **Notebook**.  

2. Na caixa de diálogo **Criar Notebook**, insira um nome amigável, escolha **Scala** como a linguagem, na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

3. Atualize os valores do ponto de extremidade, chave mestra, banco de dados e coleção para conectar a coleção de banco de dados para ler e gravar dados do Twitter.

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. Execute a consulta para obter a contagem de tweets por hashtags diferentes dos dados armazenados em cache. 

   ```scala
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

5. Crie uma nova estrutura de dados de marcas de tweets e salve os dados na nova coleção. Após executar o código a seguir, será possível retornar ao portal e verificar se os dados estão gravados na coleção. 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

O SDK do Java dá suporte aos valores a seguir para mapeamento de configuração:

|Configuração  |DESCRIÇÃO  |
|---------|---------|
| BulkImport | Um valor booliano que indica se os dados devem ser importados, usando a biblioteca BulkExecutor. Por padrão, esse valor é definido como verdadeiro. |
|WritingBatchSize  |   Indica o tamanho do lote a ser usado ao gravar dados na coleção do Azure Cosmos DB. <br/><br/> Se o parâmetro BulkImport estiver definido como verdadeiro, o parâmetro WritingBatchSize indicará o tamanho do lote de documentos fornecidos como entrada para a API importAll da biblioteca BulkExecutor. Por padrão, esse valor é definido como 100 K. <br/><br/> Se o parâmetro BulkImport estiver definido como falso, o parâmetro WritingBatchSize indicará o tamanho do lote a ser usado ao gravar na coleção do Azure Cosmos DB. O conector envia solicitações createDocument/upsertDocument de maneira assíncrona em lotes. Quanto maior o tamanho do lote, maior a taxa de transferência possível de obter, desde que os recursos do cluster estejam disponíveis. Por outro lado, especifique um tamanho de lote de número menor para limitar a taxa e o consumo de RU. Por padrão, o tamanho do lote de gravação é definido como 500.  |
|Upsert   |  Uma cadeia de valores boolianos indicando se o upsertDocument deve ser usado, em vez do CreateDocument ao gravar na coleção do CosmosDB.   |
| WriteThroughputBudget |  Uma cadeia de caracteres de inteiro que representa o número de RU\s que você quer alocar para o trabalho do Spark de ingestão em massa do total de taxa de transferência alocada para a coleção. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Gravar dados do Twitter que estão sendo transmitidos para o Azure Cosmos DB 

Nesta seção, você executará consultas do Spark para gravar o feed de alterações do streaming de dados do Twitter em uma nova coleção no mesmo banco de dados. A versão HTML do notebook é hospedada no repositório GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). É necessário baixar os arquivos do repositório e navegar até `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html` para importar o notebook para a conta do Azure Databricks, atualizar o URI da conta, a chave mestra, o banco de dados, os nomes das coleções e executá-lo ou criar o notebook, conforme a seguir:

1. Navegue até a conta do Azure Databricks e selecione a **Espaço de Trabalho** > **Criar** > **Notebook**.  

2. Na caixa de diálogo **Criar Notebook**, insira um nome amigável, escolha **Scala** como a linguagem, na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

3. Atualize os valores do ponto de extremidade, chave mestra, banco de dados e coleção para conectar a coleção de banco de dados para ler e gravar dados do Twitter.

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. Inicie a leitura do feed de alterações como um fluxo, usando o comando spark.readStream.format():
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. Defina a configuração da coleção de destino e inicie o trabalho de fluxo, usando o método writeStream.format():

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
 ```

O SDK do Java dá suporte aos valores a seguir para mapeamento de configuração:

|Configuração  |DESCRIÇÃO  |
|---------|---------|
|Upsert   |  Uma cadeia de valores boolianos indicando se o upsertDocument deve ser usado, em vez do CreateDocument ao gravar na coleção do CosmosDB.   |
|checkpointlocation  |   Um caminho para o armazenamento de arquivos local para persistir tokens de continuação no caso de falhas de nó.   |
|WritingBatchSize  |  Indica o tamanho do lote a ser usado ao gravar dados na coleção do Azure Cosmos DB. O conector envia solicitações createDocument/upsertDocument de maneira assíncrona em lotes. Quanto maior o tamanho do lote, maior a taxa de transferência possível de obter, desde que os recursos do cluster estejam disponíveis. Por outro lado, especifique um tamanho de lote de número menor para limitar a taxa e o consumo de RU. Por padrão, o tamanho do lote de gravação é definido como 500.  |


## <a name="considerations-when-using-java-sdk"></a>Considerações ao usar o SDK do Java

A conexão do Spark do Azure Cosmos DB usando o SDK do Java é recomendável nos cenários a seguir:

* Convém usar Python e/ou Scala.  

* Há uma grande quantidade de dados para transferir entre o Apache Spark e o Azure Cosmos DB, o SDK do Java tem um desempenho mais alto quando comparado ao pyDocumentDB. Para dar uma ideia da diferença de desempenho de consulta, consulte o [wiki de Execuções de Teste de Consulta](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Próximas etapas

Se você ainda não baixou, baixe o conector do Spark ao Azure Cosmos DB no repositório GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) e explore os recursos adicionais no repositório:

* [Exemplos de agregações distribuídas](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exemplos de scripts e notebooks](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Convém examinar o [Guia do Apache Spark SQL, quadros de dados e conjuntos de dados](http://spark.apache.org/docs/latest/sql-programming-guide.html) e o artigo [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
