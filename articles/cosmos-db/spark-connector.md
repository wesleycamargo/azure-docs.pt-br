---
title: Conecte o Apache Spark ao Azure Cosmos DB | Microsoft Docs
description: Saiba mais sobre o conector do Spark do Azure do Cosmos do Azure que permite conectar o Apache Spark ao Azure Cosmos DB. Você pode executar agregações distribuídas no sistema de banco de dados multilocatário distribuído globalmente da Microsoft.
keywords: apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: 62395ff5370a057ca1888dd135b83be9d6f34f8d
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51707173"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Acelere a análise de big data usando o conector do Apache Spark para o Azure Cosmos DB
 
O conector do Apache Spark para o Azure Cosmos DB permite que o Azure Cosmos DB seja uma entrada ou saída para tarefas do Apache Spark. A conexão do [Spark](http://spark.apache.org/) ao [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) acelera sua capacidade de resolver problemas de ciência de dados que se movem rapidamente. Você pode usar o Azure Cosmos DB para persistir e consultar dados rapidamente. O conector usa eficientemente os índices gerenciados do Azure Cosmos DB. Os índices permitem a atualização de colunas quando você realiza a filtragem de dados analíticos e push-down em dados distribuídos de maneira rápida e globalmente distribuídos. Esse tipo de dados pode ir da Internet of Things (IoT) aos cenários de ciência e análise de dados.

## <a name="connector-components"></a>Componentes do conector

O conector do Spark para o Azure Cosmos DB possui os seguintes componentes:

* [O Azure Cosmos DB](http://documentdb.com) permite que você provisione e elasticamente dimensione a taxa de transferência e o armazenamento, em qualquer número de regiões geográficas.  

* [O Apache Spark](http://spark.apache.org/) é um poderoso mecanismo de processamento de código aberto, construído em torno da velocidade, facilidade de uso e análise sofisticada.  

* [O cluster do Apache Spark em bancos de dados do Azure](https://docs.azuredatabricks.net/getting-started/index.html) permite que você execute tarefas do Spark no cluster do Spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Conectar Apache Spark ao Azure Cosmos DB

Há duas abordagens para conectar o Apache Spark e o Azure Cosmos DB:

- [SDK do Python do Azure Cosmos DB SQL](https://github.com/Azure/azure-documentdb-python), um conector baseado em Python, que também é referido como *pyDocumentDB*.  

- [SDK Java do Azure Cosmos DB SQL](https://github.com/Azure/azure-documentdb-java), um conector baseado em Java.


**Versões com suporte**

| Componente | Versão |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Versão de tempo de execução do Azure Databricks | > 3.4 |
| SDK de SQL Java do Azure Cosmos DB | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Conectar usando SDK do Python ou pyDocumentDB

O diagrama a seguir mostra a arquitetura da implementação do SDK pyDocumentDB:

![Diagrama do fluxo de dados do Spark para o Azure Cosmos DB por meio do pyDocumentDB DB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Fluxo de dados

O fluxo de dados da implementação do pyDocumentDB é conforme a seguir:

* O nó mestre do Spark se conecta ao nó de gateway do Azure Cosmos DB por meio de pyDocumentDB. Você especifica apenas as conexões do Spark e do Azure Cosmos DB. Conexões com os respectivos nós mestre e gateway são transparentes.  

* O nó de gateway faz a consulta no Azure Cosmos DB, onde a consulta é executada subsequentemente nas partições da coleção nos nós de dados. A resposta para essas consultas é enviada de volta ao nó de gateway e esse conjunto de resultados retorna ao nó mestre do Spark.  

* Consultas subsequentes (por exemplo, em um quadro de dados do Spark) são enviadas aos nós do trabalhador do Spark para processamento.  

A comunicação entre o Spark e o Azure Cosmos DB é limitada ao nó mestre do Spark e aos nós de gateway do Azure Cosmos DB. As consultas avança tão depressa como a camada de transporte entre esses dois nós permite.

Execute as etapas a seguir para conectar o Spark ao Azure Cosmos DB usando o SDK pyDocumentDB:

1. Crie um [workspace do Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) e um [cluster do Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). A versão 4.0 do tempo de execução do Azure Databricks inclui o Apache Spark 2.3.0 e o Scala 2.11 nesse workspace.  

2. Quando o cluster for criado e estiver em execução, vá para **Workspace** > **Crie a** > **Biblioteca**.  
3. Na caixa de diálogo New Library, escolha **Upload Python Egg ou PyPi** como a origem. Fornecer **pydocumentdb** como o nome e selecione **instalar biblioteca**. SDK do pyDocumentdb já está publicada para os pacotes de pip, para que você possa localizar e instalá-lo. 

   ![Captura de tela da caixa de diálogo Nova Biblioteca](./media/spark-connector/create-library.png)

4. Depois que a biblioteca estiver instalada, conecte-a ao cluster que você criou anteriormente.  

5. Vá para **workspace** > **crie** > **Notebook**.  

6. Na caixa de diálogo **Criar Bloco de Notas** ,insira um nome amigável e escolha **Python** como o idioma. Na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

7. Execute algumas consultas Spark usando os dados de exemplo de voos hospedados na conta "doctorwho" do Azure Cosmos DB. Esta conta é acessível publicamente. O repositório do [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hospeda o notebook [Read_Batch_PyDocumentDB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Batch_PyDocumentDB.ipynb). Você pode importar o bloco de anotações para sua conta do Azure Databricks e executá-lo. A seção a seguir explica a funcionalidade dos blocos de código em detalhes.

O snippet de código a seguir mostra como importar o SDK pyDocumentDB e executar uma consulta no contexto do Spark. Conforme observado no snippet de código, o SDK pyDocumentDB contém os parâmetros de conexão necessários para conectar a conta do Azure Cosmos DB. Ele importa as bibliotecas necessárias e configura a chave mestra e o host para criar o cliente do Azure Cosmos DB (pydocumentdb.document_client).


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

Em seguida, você pode executar consultas. O snippet de código a seguir se conecta à coleção airports.codes na conta doctorwho e executa uma consulta para extrair as cidades do aeroporto no estado de Washington. 

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

Depois de executar a consulta, o resultado será um "query_iterable.QueryIterable" que é convertido em uma lista do Python. Essa lista, por sua vez, é convertida em um dataframe do Spark. 

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

### <a name="considerations-when-using-pydocumentdb-sdk"></a>Considerações ao usar o SDK pyDocumentDB
A conexão do Spark ao Azure Cosmos DB usando o pyDocumentDB SDK é recomendada nos seguintes cenários:

* Você deseja usar Python.  

* Você está retornando um conjunto de resultados relativamente pequeno do Azure Cosmos DB para o Spark. Observe que o conjunto de dados subjacente no Banco de Dados do Azure Cosmos pode ser muito grande e você está aplicando filtros ou executando filtros de predicado na sua origem do Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Conectar usando o SDK do Java

O diagrama a seguir mostra a arquitetura da implementação do Java SDK do Azure Cosmos DB SQL e os dados são movidos entre os nós do trabalhador do Spark:

![Diagrama do fluxo de dados no conector do Spark para o Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Fluxo de dados

O fluxo de dados da implementação do SDK do Java é conforme a seguir:

* O nó mestre do Spark conecta com o nó de gateway do Azure Cosmos DB para obter o mapa de partições. Você especifica apenas as conexões do Spark e do Azure Cosmos DB. Conexões com os respectivos nós mestre e gateway são transparentes.  

* Essas informações são fornecidas de volta ao nó mestre do Spark. Neste ponto, você deve conseguir analisar a consulta para determinar as partições e suas localizações no Azure Cosmos DB que você precisa acessar.  

* Essas informações são transmitidas para os nós de trabalho do Spark.  

* Os nós do trabalhador do Spark conectam-se às partições do Azure Cosmos DB diretamente, para extrair e retornar os dados para as partições do Spark nos nós do trabalhador.  

A comunicação entre o Spark e o Azure Cosmos DB é significativamente mais rápida, porque a movimentação de dados está entre os nós do trabalhador do Spark e os nós de dados do Azure Cosmos DB (partições). Neste exemplo, você envia alguns dados do Twitter de amostra para a conta do Azure Cosmos DB e executa as consultas do Spark usando esses dados. Use as etapas a seguir para gravar dados do Twitter de exemplo no Azure Cosmos DB:

1. Crie um [conta de API de SQL do Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) e [adicione uma coleção](create-sql-api-dotnet.md#add-a-collection) à conta.  

2. Faça o download da amostra [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) do GitHub. Você usa esse feed para gravar dados do Twitter de amostra no Azure Cosmos DB.  

3. Abra um prompt de comando e instale os módulos Tweepy e pyDocumentdb executando os seguintes comandos:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Extraia o conteúdo da amostra de feed do Twitter e abra o arquivo config.py. Atualize os valores de masterKey, host, databaseId, collectionId e preferredLocations.  

5. Vá para `http://apps.twitter.com/` e registre o aplicativo de feed do Twitter. Após escolher um nome para o aplicativo, você receberá uma **chave do consumidor, segredo do consumidor, token de acesso e segredo do token de acesso** . Copie esses valores e atualize-os no arquivo config.py para fornecer o acesso programático do aplicativo de feed do Twitter ao Twitter.   

6. Salve o arquivo config.py. Abra um prompt de comando e execute o aplicativo Python usando o seguinte comando:

   ```bash
   Python driver.py
   ```

7. Vá para a coleção do Azure Cosmos DB no portal e verifique se os dados do Twitter são gravados na coleção.

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>Localizar e anexar o SDK do Java para o cluster Spark

1. Crie um [workspace do Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) e um [cluster do Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). A versão 4.0 do tempo de execução do Azure Databricks inclui o Apache Spark 2.3.0 e o Scala 2.11 nesse workspace.  

2. Quando o cluster for criado e estiver em execução, vá para **Workspace** > **Crie a** > **Biblioteca**.  

3. Na caixa de diálogo **New Library**, escolha **Maven Coordinate** como a origem. Forneça o valor de coordenada **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0** e selecione **Criar Biblioteca**. As dependências do Maven são resolvidas e o pacote é adicionado ao workspace. No formato de coordenadas do Maven anterior, 2.3.0 representa a versão do Spark, 2.11 representa a versão do Scala e 1.2.0 representa a versão do conector do Azure Cosmos DB. 

4. Depois que a biblioteca estiver instalada, conecte-a ao cluster que você criou anteriormente. 

Este artigo demonstra o uso do Java SDK do conector Spark nos seguintes cenários:

* Leia os dados do Twitter do Azure Cosmos DB.  

* Leia os dados do Twitter que estão sendo transmitidos para o Azure Cosmos DB.  

* Grave dados do Twitter no Azure Cosmos DB. 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Ler dados do Twitter do Azure Cosmos DB
 
Nesta seção, você executa as consultas do Spark para ler um lote de dados do Twitter do Azure Cosmos DB. O repositório do GitHub do [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hospeda o bloco de notas [Read_Batch_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Batch_Twitter_Data.ipynb). Você pode importar o bloco de anotações para sua conta do Azure Databricks e atualizar os nomes de URI, chave mestra, banco de dados e coleção da conta. Você pode executar o bloco de notas ou criá-lo da seguinte maneira:

1. Vá para a sua conta do Azure Databricks e selecione **Workspace** > **Crie o** > **Notebook**. 

2. Na caixa de diálogo **Criar Bloco de Notas** ,insira um nome amigável e escolha **Python** como o idioma. Na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

3. Atualize os valores de terminal, chave mestra, banco de dados e coleta para se conectar à conta. Leia os tweets usando o comando spark.read.format ().

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
|query_maxdegreeofparallelism  | Define o número de operações simultâneas executadas no lado do cliente durante a execução da consulta paralela. Se estiver definido para um valor maior que 0, limita o número de operações simultâneas ao valor atribuído. Se ele for definido como menor que 0, o sistema decide automaticamente o número de operações simultâneas para executar. Como o conector mapeia cada partição de coleção com um executor, esse valor não terá nenhum efeito na operação de leitura.        |
|query_maxbuffereditemcount     |    Define o número máximo de itens que podem ser armazenados em buffer no lado do cliente durante a execução da consulta paralela. Se estiver definido para um valor maior que 0, ele limitará o número de itens em buffer ao valor atribuído. Se estiver definido para menos de 0, o sistema decide automaticamente o número de itens a serem armazenados em buffer.     |
|query_enablescan    |   Define a opção para ativar as verificações nas consultas que não puderam ser exibidas porque a indexação foi desativada nos caminhos solicitados.       |
|query_disableruperminuteusage  |  Desativa as unidades de solicitação (RUs) / capacidade de minuto para atender à consulta, se as RUs / segundo fornecidas regularmente forem esgotadas.       |
|query_emitverbosetraces   |   Define a opção para permitir que as consultas emitam rastreios detalhados para investigação.      |
|query_pagesize  |   Define o tamanho da página de resultados da consulta para cada solicitação de consulta. Para otimizar o rendimento, use um tamanho de página grande para reduzir o número de viagens de ida e volta para buscar resultados.      |
|query_custom  |  Define a consulta do Azure Cosmos DB para substituir a consulta padrão ao buscar dados do Azure Cosmos DB. Observe que, quando você fornece esse valor, ele é usado no lugar de uma consulta com predicados descendentes.     |

Dependendo do cenário, você deve usar valores de configuração diferentes para otimizar o desempenho e a taxa de transferência. Observe que a chave de configuração não faz distinção entre maiúsculas e minúsculas e o valor de configuração é sempre uma cadeia de caracteres.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Leia os dados do Twitter que estão sendo transmitidos para o Azure Cosmos DB

Nesta seção, você executa as consultas do Spark para ler um feed de mudança de dados do Twitter. Enquanto você executa as consultas nesta seção, verifique se o aplicativo de feed do Twitter está em execução e enviando dados para o Azure Cosmos DB. O repositório do GitHub do [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hospeda o bloco de notas [Read_Stream_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Stream_Twitter_Data.scala). Você pode importar o bloco de anotações para sua conta do Azure Databricks e atualizar os nomes de URI, chave mestra, banco de dados e coleção da conta. Você pode executar o bloco de notas ou criá-lo da seguinte maneira:

1. Vá para a sua conta do Azure Databricks e selecione **Workspace** > **Crie o** > **Notebook**.  

2. No **Criar bloco de notas** caixa de diálogo, digite um nome amigável e escolha **Scala** como o idioma. Na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

3. Atualize os valores de terminal, chave mestra, banco de dados e coleta para se conectar à conta.

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
4. Comece a ler o feed de mudança como um fluxo usando o comando spark.readStream.format ():

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Inicie a consulta para o console de streaming:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

O SDK do Java dá suporte aos valores a seguir para mapeamento de configuração:

|Configuração  |DESCRIÇÃO  |
|---------|---------|
|readchangefeed   |  Indica que o conteúdo da coleção é buscado no Feed de Alterações do CosmosDB. O valor padrão é falso.       |
|changefeedqueryname |   Uma cadeia de caracteres personalizada para identificar a consulta. O conector controla os tokens de continuação de coleção para diferentes consultas de alterações de feed separadamente. Se readchangefeed for true, essa é uma configuração necessária que não pode receber valor vazio.      |
|changefeedcheckpointlocation  |   Um caminho para o armazenamento de arquivos local para persistir tokens de continuação no caso de falhas de nó.      |
|changefeedstartfromthebeginning  |  Define se o feed de alterações deve iniciar do começo (verdadeiro) ou do ponto atual (falso). Por padrão, ele inicia a partir do atual (falso).       |
|rollingchangefeed  |   Um valor booliano que indica se o feed de alterações deve ser da última consulta. O valor padrão é false, o que significa que as alterações são contadas a partir da primeira leitura da coleção.      |
|changefeedusenexttoken  |   Um valor booliano para dar suporte a cenários de falha de processamento. Indica que o lote de feeds de mudança atual foi tratado normalmente. O conjunto de dados distribuído resiliente deve usar os próximos tokens de continuação para obter o lote subsequente de alterações.      |
| InferStreamSchema | Um valor booleano que indica se o esquema dos dados de streaming deve ser amostrado no início da transmissão. Por padrão, esse valor é definido como verdadeiro. Se esse parâmetro for definido como verdadeiro e o esquema dos dados de streaming for alterado depois que os dados forem amostrados, as propriedades adicionadas recentemente serão removidas na estrutura de dados de streaming. <br/><br/> Se você quiser que a estrutura de dados de streaming seja independente de esquema, defina esse parâmetro para falso. Nesse modo, o corpo dos documentos lidos do feed de alteração do Azure Cosmos DB é empacotado em uma propriedade do corpo. Essa propriedade está no quadro de dados de fluxo contínuo resultante, além dos valores da propriedade do sistema.
 |

### <a name="connection-settings"></a>Configurações de conexão

O SDK do Java dá suporte às configurações de conexão a seguir:

|Configuração  |DESCRIÇÃO  |
|---------|---------|
|connectionmode   |  Define o modo de conexão que o DocumentClient interno deve usar para comunicar-se com o Azure Cosmos DB. Os valores permitidos são **DirectHttps** (valor padrão) e **Gateway**. O modo de conexão DirectHttps encaminha as solicitações diretamente para as partições do Cosmos DB e fornece alguma vantagem de latência.       |
|connectionmaxpoolsize   |  Define o valor do tamanho do pool de conexões usado pelo DocumentClient interno. O valor padrão é 100.       |
|connectionidletimeout  |  Define o valor de tempo limite, em segundos, para conexões inativas. O valor padrão é 60.       |
|query_maxretryattemptsonthrottledrequests    |  Define o número máximo de repetições. Você usa esse valor no caso de uma falha de solicitação devido à limitação de taxa no cliente. Se não for especificado, o valor padrão será 1000 tentativas de repetição.       |
|query_maxretrywaittimeinseconds   |  Define o tempo máximo de repetição em segundos. Por padrão, é de 1000 segundos.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Escrever dados do Twitter no Azure Cosmos DB 

Nesta seção, você executa as consultas do Spark para gravar um lote de dados do Twitter em uma nova coleção no mesmo banco de dados. O repositório do GitHub do [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hospeda o bloco de notas [Read_Batch_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Write_Batch_Twitter_Data.ipynb). Você pode importar o bloco de anotações para sua conta do Azure Databricks e atualizar os nomes de URI, chave mestra, banco de dados e coleção da conta. Você pode executar o bloco de notas ou criá-lo da seguinte maneira:

1. Vá para a sua conta do Azure Databricks e selecione **Workspace** > **Crie o** > **Notebook**.  

2. No **Criar bloco de notas** caixa de diálogo, digite um nome amigável e escolha **Scala** como o idioma. Na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

3. Atualize os valores de terminal, chave mestra, banco de dados e coleta para conectar-se à coleta de banco de dados para ler e gravar dados do Twitter.

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

5. Crie um novo quadro de dados de tags de tweets e salve os dados na nova coleção. Após executar o código a seguir, será possível retornar ao portal e verificar se os dados estão gravados na coleção. 

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
|WritingBatchSize  |   Indica o tamanho do lote a ser usado quando você está gravando dados na coleção do Azure Cosmos DB. <br/><br/> Se o parâmetro BulkImport estiver definido como verdadeiro, o parâmetro WritingBatchSize indicará o tamanho do lote de documentos fornecidos como entrada para a API importAll da biblioteca BulkExecutor. Por padrão, esse valor é definido como 100 K. <br/><br/> Se o parâmetro BulkImport estiver definido como false, o parâmetro WritingBatchSize indicará o tamanho do lote a ser usado durante a gravação na coleção do Azure Cosmos DB. O conector envia solicitações createDocument e upsertDocument de forma assíncrona em lote. Quanto maior o tamanho do lote, maior o rendimento que você pode obter, contanto que os recursos do cluster estejam disponíveis. Por outro lado, especifique um tamanho de lote de número menor para limitar a taxa e o consumo de RU. Por padrão, o tamanho do lote de gravação é definido como 500.  |
|Upsert   |  Uma string de valor booleano que indica se você deve usar upsertDocument em vez de CreateDocument quando estiver gravando na coleção do Azure Cosmos DB.   |
| WriteThroughputBudget |  Uma cadeia de inteiros que representa o número de RUs que você deseja alocar para a tarefa de ignição em massa Spark, fora do throughput total alocado para a coleção. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Escrever dados do Twitter que estão sendo transmitidos para o Azure Cosmos DB 

Nesta seção, você executa as consultas do Spark para gravar um feed de mudança de dados do Twitter de streaming em uma nova coleção no mesmo banco de dados. O repositório do GitHub do [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) hospeda o bloco de notas [Read_Stream_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Write_Stream_Data.scala). Você pode importar o bloco de anotações para sua conta do Azure Databricks e atualizar os nomes de URI, chave mestra, banco de dados e coleção da conta. Você pode executar o bloco de notas ou criá-lo da seguinte maneira:

1. Vá para a sua conta do Azure Databricks e selecione **Workspace** > **Crie o** > **Notebook**.  

2. No **Criar bloco de notas** caixa de diálogo, digite um nome amigável e escolha **Scala** como o idioma. Na lista suspensa, selecione o cluster que você criou anteriormente e selecione **Criar**.  

3. Atualize os valores de terminal, chave mestra, banco de dados e coleta para conectar-se à coleta de banco de dados para ler e gravar dados do Twitter.

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
4. Comece a ler o feed de mudança como um fluxo usando o comando spark.readStream.format ():
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. Defina a configuração da coleção de destino e inicie a tarefa de fluxo usando o método writeStream.format ():

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
|Upsert   |  Uma string de valor booleano que indica se você deve usar upsertDocument em vez de CreateDocument quando estiver gravando na coleção do Azure Cosmos DB.   |
|checkpointlocation  |   Um caminho para o armazenamento de arquivos local para persistir tokens de continuação no caso de falhas de nó.   |
|WritingBatchSize  |  Indica o tamanho do lote a ser usado ao gravar dados na coleção do Azure Cosmos DB. O conector envia solicitações createDocument e upsertDocument de forma assíncrona em lote. Quanto maior o tamanho do lote, maior o rendimento que você pode obter, contanto que os recursos do cluster estejam disponíveis. Por outro lado, especifique um tamanho de lote de número menor para limitar a taxa e o consumo de RU. Por padrão, o tamanho do lote de gravação é definido como 500.  |


### <a name="considerations-when-using-java-sdk"></a>Considerações ao usar o SDK do Java

A conexão do Spark ao Azure Cosmos DB usando o Java SDK é recomendada nos seguintes cenários:

* Você deseja usar Python ou Scala.  

* Você tem uma grande quantidade de dados para transferir entre o Apache Spark e o Azure Cosmos DB. O SDK do Java é melhor do que o pyDocumentDB. Para obter mais informações sobre a diferença de desempenho de consulta, consulte a [wiki de execuções de teste de consulta](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, faça o download do conector do Spark para o Azure Cosmos DB no repositório [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) do GitHub. Explore os recursos adicionais no repositório a seguir:

* [Exemplos de agregações](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Exemplos de scripts e bloco de notas](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Você também pode revisar o [Apache Spark SQL, o DataFrames e o Guia de Conjuntos de Dados](http://spark.apache.org/docs/latest/sql-programming-guide.html) e o artigo [Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
