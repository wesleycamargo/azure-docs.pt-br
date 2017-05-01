---
title: "Conexão do Apache Spark com o Azure DocumentDB | Microsoft Docs"
description: "Use este tutorial para saber mais sobre o conector Spark do Azure DocumentDB que permite a conexão do Apache Spark ao Azure DocumentDB para executar agregações distribuídas e ciências de dados no sistema de banco de dados multilocatário distribuído globalmente da Microsoft e projetado para a nuvem."
keywords: apache spark
services: documentdb
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: denlee
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 3d534ed667a8d8d012fa35152ae1bdb873e9672d
ms.lasthandoff: 04/06/2017


---

# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-documentdb-connector"></a>Acelerar a análise de big-data em tempo real com o conector do Spark com o DocumentDB

O conector do Spark com o DocumentDB permite que o Azure DocumentDB aja como uma fonte de entrada ou coletor de saída para trabalhos do Apache Spark. A conexão do [Spark](http://spark.apache.org/) ao [DocumentDB](https://azure.microsoft.com/services/documentdb/) acelera sua capacidade de resolver os problemas em constante mudança com a ciência de dados, em que os dados podem ser persistidos rapidamente e consultados usando o DocumentDB. O conector do Spark com o DocumentDB utiliza com eficiência os índices nativos gerenciados pelo DocumentDB e permite colunas atualizáveis ao executar a análise, a aplicação de filtragem de predicado em dados distribuídos globalmente e em constante mudança, variando de IoT, ciência de dados e cenários de análise. 

## <a name="download"></a>Baixar

Comece baixando conector do Spark com o DocumentDB (visualização) do repositório [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark/) no GitHub.

## <a name="connector-components"></a>Componentes do conector

O conector usa os seguintes componentes:

* O [DocumentDB](http://documentdb.com), um [sistema de banco de dados multilocatários, distribuído globalmente](documentdb-distribute-data-globally.md) da Microsoft e desenvolvido para a nuvem. O DocumentDB permite que os clientes provisionem e dimensionem elasticamente a produtividade e o armazenamento em qualquer quantidade de regiões geográficas. O serviço oferece garantia de 99% de baixa latência, uma garantia de 99,99% de alta disponibilidade e [vários modelos de consistência bem definidos](documentdb-consistency-levels.md) para desenvolvedores.

* [Apache Spark](http://spark.apache.org/), que é um mecanismo de processamento eficiente com código-fonte aberto criado pensando em velocidade, facilidade de uso e análise sofisticada. 

* [Apache Spark no Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Implante o Apache Spark na nuvem para implantações críticas usando o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Versões com suporte oficial:

| Componente | Versão |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| SDK de Java do Azure DocumentDB | 1.9.6 |
 
Este artigo ajuda você a executar alguns exemplos simples com Python (via pyDocumentDB) e a interface scala.

Há duas abordagens para conectar o Apache Spark e o Azure DocumentDB:
- Usar pyDocumentDB por meio do [SDK de Python do Azure DocumentDB](https://github.com/Azure/azure-documentdb-python).
- Criar um conector do Spark com o DocumentDB baseado em Java utilizando o [SDK de Java do Azure DocumentDB](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>implementação de pyDocumentDB 
O [SDK do pyDocumentDB](https://github.com/Azure/azure-documentdb-python) atual possibilita a conexão do Spark ao DocumentDB, conforme mostra o diagrama a seguir:

![Fluxo de dados do Spark ao DocumentDB por meio de pyDocumentDB](./media/documentdb-spark-connector/azure-documentdb-spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Fluxo de dados da implementação do pyDocumentDB

O fluxo de dados é o seguinte:

1. A conexão é feita do nó mestre do Spark com o nó de gateway do DocumentDB por meio de pyDocumentDB.  Observe que o usuário especifica apenas as conexões do Spark ao DocumentDB, o fato de que ele se conecta ao respectivo mestre e nós de gateway é transparente ao usuário.
2. A consulta é feita no DocumentDB (por meio do nó de gateway), onde a consulta executa posteriormente a consulta em partições da coleção nos nós de dados. A resposta para essas consultas é enviada de volta ao nó de gateway e esse conjunto de resultados retorna ao nó mestre do Spark.
3. Todas as consultas subsequentes (por exemplo, em um DataFrame do Spark) são enviadas aos nós de trabalho do Spark para processamento.

O detalhe importante é que a comunicação entre o Spark e o DocumentDB é limitada ao nó mestre do Spark e aos nós de gateway do DocumentDB.  As consultas são mais ágeis, pois a camada de transporte ocorre entre esses dois nós.

### <a name="installing-pydocumentdb"></a>Instalação do pyDocumentDB
Instale o pyDocumentDB em seu nó de driver usando **pip**, por exemplo:

```
pip install pyDocumentDB
```


### <a name="connecting-spark-to-documentdb-via-pydocumentdb"></a>Conexão do Spark ao DocumentDB por meio de pyDocumentDB 
Graças à simplicidade do transporte da comunicação, a execução de uma consulta do Spark para o DocumentDB usando pyDocumentDB é relativamente simples.

O trecho de código a seguir mostra como usar pyDocumentDB dentro de um contexto do Spark.

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery 
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to DocumentDB 
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==' 
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Conforme observado no trecho de código:

* O SDK de Python do DocumentDB contém todos os parâmetros de conexão necessários, incluindo as localizações preferenciais (isto é, qual réplica de leitura em qual ordem de prioridade).
*  Importe as bibliotecas necessárias e configure sua **masterKey** e **host** a fim de criar o *cliente* do DocumentDB (**pydocumentdb.document_client**).


### <a name="executing-spark-queries-via-pydocumentdb"></a>Executar consultas Spark via pyDocumentDB
Os exemplos a seguir usam a instância do DocumentDB criada no trecho anterior usando as chaves somente leitura especificadas.  O trecho de código a seguir se conecta à coleção **airports.codes** (na conta DoctorWho, conforme especificado anteriormente) executando uma consulta para extrair as cidades com aeroporto no estado de Washington. 

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the DocumentDB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Após a execução da consulta por meio de **consulta**, o resultado será um **query_iterable. QueryIterable** que é convertido em uma lista de Python. Uma lista de Python pode ser facilmente convertida em um DataFrame do Spark usando o seguinte código:

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-documentdb"></a>Por que usar o pyDocumentDB para conectar o Spark ao DocumentDB?
A conexão do Spark ao DocumentDB usando pyDocumentDB normalmente ocorre para cenários nos quais:

* Você deseja usar python.
* Você está retornando um conjunto de resultados relativamente pequeno do DocumentDB para o Spark.  Observe que o conjunto de dados subjacente dentro do DocumentDB pode ser muito grande. É mais do que aplicar filtros - ou seja, executar filtros de predicado - em sua fonte do DocumentDB.  

## <a name="spark-to-documentdb-connector"></a>Conector do Spark ao DocumentDB

O conector do Spark ao DocumentDB utiliza o [SDK de Java do Azure DocumentDB](https://github.com/Azure/azure-documentdb-java) e move dados entre os nós de trabalho do Spark e o DocumentDB, conforme mostra o diagrama a seguir:

![Fluxo de dados no conector do Spark ao DocumentDB](./media/documentdb-spark-connector/azure-documentdb-spark-connector.png)

O fluxo de dados é o seguinte:

1. Uma conexão é feita a partir do nó mestre do Spark para o nó do gateway do DocumentDB para obter o mapa de partições.  Observe que o usuário especifica apenas as conexões do Spark ao DocumentDB, o fato de que ele se conecta ao respectivo mestre e nós de gateway é transparente ao usuário.
2. Essas informações são fornecidas de volta ao nó mestre do Spark.  Neste ponto, você deve ser capaz de analisar a consulta para determinar quais partições (e suas localizações) dentro do DocumentDB você precisa acessar.
3. Essas informações são transmitidas para os nós de trabalho do Spark...
4. Permitindo assim que os nós de trabalho do Spark se conectem diretamente às partições do DocumentDB para extrair os dados necessários e trazer os dados de volta para as partições do Spark dentro dos nós de trabalho do Spark.

O detalhe importante é que a comunicação entre o Spark e o DocumentDB seja consideravelmente mais rápida, pois a movimentação de dados ocorre entre os nós de trabalho do Spark e os nós de dados do DocumentDB (partições).

### <a name="building-the-spark-to-documentdb-connector"></a>Compilação do conector do Spark ao DocumentDB
Atualmente, o projeto do conector usa maven. Para compilar o conector sem dependências, execute:
```
mvn clean package
```
Você também pode baixar as versões mais recentes dos arquivos jar dentro da pasta *releases*.

### <a name="including-the-azure-documentdb-spark-jar"></a>Inclusão do JAR de Spark e DocumentDB
Antes de executar qualquer código, primeiro você precisa incluir o JAR de Spark e DocumentDB.  Se você estiver usando o **spark-shell**, você poderá incluir o JAR usando a opção **--jars**.  

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1-jar-with-dependencies.jar
```

ou, se você quiser executar o jar sem dependências:

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1.jar,/$location/azure-documentdb-1.9.6.jar
```

Se você estiver usando um serviço de notebook, como Jupyter do Azure HDInsight, use os comandos **spark magic**:

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.9.6.jar","wasb:///example/jars/azure-documentdb-spark-0.0.1.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

O comando **jars** permite que você inclua os dois jars necessários para **azure-documentdb-spark** (o próprio e o SDK de Java do Azure DocumentDB) e exclui **scala-reflect** para que ele não interfira com as chamadas do Livy feitas (notebook Jupyter > Livy > Spark).

### <a name="connecting-spark-to-documentdb-using-the-connector"></a>Conexão do Spark ao DocumentDB usando o conector
Embora o transporte de comunicação seja um pouco mais complicado, a execução de uma consulta do Spark para o DocumentDB usando o conector é consideravelmente mais rápida.

O trecho de código a seguir mostra como usar o conector dentro de um contexto do Spark.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))
 
// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Conforme observado no trecho de código:

- O **azure-documentdb-spark** contém todos os parâmetros de conexão necessários, incluindo as localizações preferenciais (por exemplo, qual réplica de leitura em qual ordem de prioridade).
- Basta importar as bibliotecas necessárias e configure sua masterKey e host a fim de criar o cliente do DocumentDB.

### <a name="executing-spark-queries-via-the-connector"></a>Executar consultas Spark por meio do conector

O exemplo a seguir usa a instância do DocumentDB criada no trecho anterior usando as chaves somente leitura especificadas. O trecho de código a seguir conecta-se à coleção DepartureDelays.flights_pcoll (na conta DoctorWho, conforme especificado anteriormente) executando uma consulta para extrair as informações de atraso de voo partindo de Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-documentdb-connector-implementation"></a>Por que usar a implementação do conector do Spark para o DocumentDB?

A conexão do Spark ao DocumentDB usando o conector normalmente ocorre para cenários nos quais:

* Você deseja usar Scala (e atualizá-lo para incluir um wrapper de Python, conforme observado em [Problema 3: adicionar wrapper de Python e exemplos](https://github.com/Azure/azure-documentdb-spark/issues/3)).
* Você tem uma grande quantidade de dados para transferência entre o Apache Spark e o DocumentDB.

Para dar uma ideia da diferença de desempenho da consulta, confira o [wiki de Execuções de teste de consulta](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Exemplo de agregação distribuída
Esta seção fornece alguns exemplos de como você pode fazer agregações distribuídas e análises usando o Apache Spark e o Azure DocumentDB.  Observe que o Azure DocumentDB já tem suporte para agregações, conforme discutido no [blog Planet scale aggregates with Azure DocumentDB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) Portanto, veja como levá-lo ao próximo nível com o Apache Spark.

Observe que essas agregações fazem referência ao [notebook do conector do Spark ao DocumentDB](https://github.com/Azure/azure-documentdb-spark/blob/master/samples/notebooks/Spark-to-DocumentDB_Connector.ipynb).

### <a name="connecting-to-flights-sample-data"></a>Conexão com dados de exemplo de voos
Para esses exemplos de agregações, estamos acessando alguns dados de desempenho de voo armazenados em nosso banco de dados do DocumentDB **DoctorWho**.  Para se conectar a ele, você precisa utilizar o trecho de código a seguir:

```
// Import Spark to DocumentDB connector
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Connect to DocumentDB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))

// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Com esse trecho de código, também vamos executar uma consulta de base que transfere o conjunto de dados filtrado que desejamos do DocumentDB para o Spark (em que o último pode executar agregações distribuídas).  Nesse caso, estamos pedindo voos partindo de Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Os resultados a seguir foram gerados pela execução das consultas do serviço de notebook do Jupyter.  Observe que todos os trechos de código são genéricos e não são específicos a qualquer serviço.

### <a name="running-limit-and-count-queries"></a>Execução de consultas LIMIT e COUNT
Assim como você estava acostumado no SQL/Spark SQL, vamos começar com uma consulta **LIMIT**:

![Consulta LIMIT do Spark](./media/documentdb-spark-connector/azure-documentdb-spark-sql-query.png)

A próxima consulta é uma consulta **COUNT** simples e rápida:

![Consulta COUNT do Spark](./media/documentdb-spark-connector/azure-documentdb-spark-count-query.png)

### <a name="group-by-query"></a>Consulta GROUP BY
Neste próximo conjunto, podemos executar facilmente consultas **GROUP BY** em nosso banco de dados do DocumentDB:

```
select destination, sum(delay) as TotalDelays 
from originSEA 
group by destination 
order by sum(delay) desc limit 10
```

![Gráfico da consulta Spark GROUP BY do Spark](./media/documentdb-spark-connector/azure-documentdb-group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Consulta DISTINCT, ORDER BY
E aqui está uma consulta **DISTINCT, ORDER BY**:

![Gráfico da consulta Spark GROUP BY do Spark](./media/documentdb-spark-connector/azure-documentdb-order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Continuar a análise de dados de voo
Você pode usar os seguintes exemplos de consultas para continuar a análise dos dados de voo:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Cinco principais destinos atrasados (cidades) partindo de Seattle
```
select destination, sum(delay) 
from originSEA
where delay < 0 
group by destination 
order by sum(delay) limit 5
```
![Gráfico dos principais atrasos do Spark](./media/documentdb-spark-connector/azure-documentdb-top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Calcular a média de atrasos por cidades de destino partindo de Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay 
from originSEA
where delay < 0 
group by destination 
order by percentile_approx(delay, 0.5)
```

![Gráfico da média de atrasos do Spark](./media/documentdb-spark-connector/azure-documentdb-median-delays-graph.png)

## <a name="next-steps"></a>Próximas etapas

Se ainda não o fez, baixe o conector do Spark ao DocumentDB do repositório [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark) do GitHub e explore os recursos adicionais no repositório:

* [Exemplos de agregações distribuídas](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Exemplos de scripts e notebooks](https://github.com/Azure/azure-documentdb-spark/tree/master/samples)

Convém examinar o [Guia do Apache Spark SQL, quadros de dados e conjuntos de dados](http://spark.apache.org/docs/latest/sql-programming-guide.html) e o artigo [Apache Spark no Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md).



