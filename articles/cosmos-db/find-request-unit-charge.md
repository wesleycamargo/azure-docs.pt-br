---
title: Localizar o encargo de RU (Unidade de Solicitação) no Azure Cosmos DB
description: Saiba como localizar o encargo de unidades de solicitação para qualquer operação executada em um contêiner do Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2019
ms.author: thweiss
ms.openlocfilehash: e3175ee136057c695ceef3cd1976b447a529c803
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59053036"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Localizar o encargo de RU (Unidade de Solicitação) no Azure Cosmos DB

Este artigo apresenta as diferentes maneiras de localizar o consumo de [unidades de solicitação](request-units.md) para qualquer operação executada em um contêiner do Azure Cosmos. Atualmente, é possível medir esse consumo usando o portal do Azure ou inspecionando a resposta enviada do Azure Cosmos DB por meio de um dos SDKs.

## <a name="core-api"></a>API principal

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

No momento, o portal do Azure permite localizar o encargo de solicitação somente para uma consulta SQL.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos DB](create-sql-api-dotnet.md#create-account) e alimente-a com alguns dados ou selecione uma conta existente que já contenha dados.

1. Abra o painel do **Data Explorer** e selecione o contêiner no qual deseja trabalhar.

1. Clique em **Nova Consulta SQL**.

1. Insira uma consulta válida e, em seguida, clique em **Executar Consulta**.

1. Clique em **Estatísticas da Consulta** para exibir o encargo de solicitação real para a solicitação recém-executada.

![Captura de tela do encargo de solicitação de consulta SQL no portal do Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Usar o SDK do .NET V2

Os objetos retornados do [SDK do .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (confira [este Início Rápido](create-sql-api-dotnet.md) sobre seu uso) expõem uma propriedade `RequestCharge`.

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="use-the-java-sdk"></a>Usar o SDK do Java

Os objetos retornados do [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (confira [este Início Rápido](create-sql-api-java.md) sobre seu uso) expõem um método `getRequestCharge()`.

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

### <a name="use-the-nodejs-sdk"></a>Usar o SDK do Node.js

Os objetos retornados do [SDK do Node.js](https://www.npmjs.com/package/@azure/cosmos) (confira [este Início Rápido](create-sql-api-nodejs.md) sobre seu uso) expõem um subobjeto `headers` que mapeia todos os cabeçalhos retornados pela API HTTP subjacente. O encargo de solicitação está disponível na chave `x-ms-request-charge`.

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

### <a name="use-the-python-sdk"></a>Usar o SDK do Python

O objeto `CosmosClient` do [SDK do Python](https://pypi.org/project/azure-cosmos/) (confira [este Início Rápido](create-sql-api-python.md) sobre seu uso) expõe um dicionário `last_response_headers` que mapeia todos os cabeçalhos retornados pela API HTTP subjacente para a última operação executada. O encargo de solicitação está disponível na chave `x-ms-request-charge`.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

O encargo de unidades de solicitação é exposto por um [comando de banco de dados](https://docs.mongodb.com/manual/reference/command/) personalizado chamado `getLastRequestStatistics`. Esse comando retorna um documento que contém o nome da última operação executada, seu encargo de solicitação e sua duração.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

No momento, o portal do Azure permite localizar o encargo de solicitação somente para uma consulta.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos DB](create-mongodb-dotnet.md#create-a-database-account) e alimente-a com alguns dados ou selecione uma conta existente que já contenha dados.

1. Abra o painel do **Data Explorer** e selecione a coleção na qual deseja trabalhar.

1. Clique em **Nova Consulta**.

1. Insira uma consulta válida e, em seguida, clique em **Executar Consulta**.

1. Clique em **Estatísticas da Consulta** para exibir o encargo de solicitação real para a solicitação recém-executada.

![Captura de tela do encargo de solicitação de consulta MongoDB no portal do Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Usar o driver do .NET do MongoDB

Ao usar o [driver oficial do .NET do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/) (confira [este Início Rápido](create-mongodb-dotnet.md) sobre seu uso), os comandos podem ser executados por meio da chamada do método `RunCommand` em um objeto `IMongoDatabase`. Esse método exige uma implementação da classe abstrata `Command<>`.

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

### <a name="use-the-mongodb-java-driver"></a>Usar o driver do Java do MongoDB

Ao usar o [driver oficial do Java do MongoDB](http://mongodb.github.io/mongo-java-driver/) (confira [este Início Rápido](create-mongodb-java.md) sobre seu uso), os comandos podem ser executados por meio da chamada do método `runCommand` em um objeto `MongoDatabase`.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>Usar o driver do Node.js do MongoDB

Ao usar o [driver oficial do Node.js do MongoDB](https://mongodb.github.io/node-mongodb-native/) (confira [este Início Rápido](create-mongodb-nodejs.md) sobre seu uso), os comandos podem ser executados por meio da chamada do método `command` em um objeto `Db`.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>API Cassandra

Ao executar operações na API do Cassandra do Azure Cosmos DB, o encargo de unidades de solicitação é retornado no conteúdo de entrada como um campo chamado `RequestCharge`.

### <a name="use-the-net-sdk"></a>Usar o SDK .NET

Ao usar o [SDK do .NET](https://www.nuget.org/packages/CassandraCSharpDriver/) (confira [este Início Rápido](create-cassandra-dotnet.md) sobre seu uso), o conteúdo de entrada pode ser recuperado na propriedade `Info` de um objeto `RowSet`.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Usar o SDK do Java

Ao usar o [SDK do Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (confira [este Início Rápido](create-cassandra-java.md) sobre seu uso), o conteúdo de entrada pode ser recuperado por meio da chamada do método `getExecutionInfo()` em um objeto `ResultSet`.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>API do Gremlin

### <a name="use-drivers-and-sdk"></a>Usar drivers e o SDK

Os cabeçalhos retornados pela API do Gremlin são mapeados para atributos de status personalizados que atualmente são exibidos pelo SDK do Java e do .NET do Gremlin. O encargo de solicitação está disponível na chave `x-ms-request-charge`.

### <a name="use-the-net-sdk"></a>Usar o SDK .NET

Ao usar o [SDK do .NET do Gremlin](https://www.nuget.org/packages/Gremlin.Net/) (confira [este Início Rápido](create-graph-dotnet.md) sobre seu uso), os atributos de status ficam disponíveis na propriedade `StatusAttributes` do objeto `ResultSet<>`.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Usar o SDK do Java

Ao usar o [SDK do Java do Gremlin](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (confira [este Início Rápido](create-graph-java.md) sobre seu uso), os atributos de status podem ser recuperados por meio da chamada do método `statusAttributes()` no objeto `ResultSet`.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>API de Tabela

No momento, o único SDK que retorna o encargo de unidades de solicitação para operações de tabela é o [SDK do .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (confira [este Início Rápido](create-table-dotnet.md) sobre seu uso). O objeto `TableResult` expõe uma propriedade `RequestCharge` que é populada pelo SDK quando usada na API de Tabela do Azure Cosmos DB.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre como otimizar o consumo de unidades de solicitação:

* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](optimize-cost-queries.md)