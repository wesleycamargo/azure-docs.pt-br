---
title: Criar um contêiner no Azure Cosmos DB
description: Saiba como criar um contêiner no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/17/2019
ms.author: rimman
ms.openlocfilehash: c075a801a877309709258dd6466e68e46d802eff
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680427"
---
# <a name="create-an-azure-cosmos-container"></a>Criar um contêiner do Azure Cosmos

Este artigo explica as diferentes maneiras de criar um contêiner do Azure Cosmos (coleção, tabela ou grafo). Para esse fim, você pode usar o portal do Azure, a CLI do Azure ou SDKs compatíveis. Este artigo demonstra como criar um contêiner, especificar a chave de partição e a taxa de transferência de provisionamento.

## <a name="create-a-container-using-azure-portal"></a>Criar um contêiner usando o portal do Azure

### <a id="portal-sql"></a>API do SQL

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Nova Coleção**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID de coleção.
   * Insira uma chave de partição.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

![Captura de tela do painel Data Explorer, com Nova coleção realçado](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>API do Azure Cosmos DB para MongoDB

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Nova Coleção**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID de coleção.
   * Insira uma chave de fragmento.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

![Captura de tela da API Azure Cosmos DB para MongoDB, caixa de diálogo Adicionar Coleção](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>API do Cassandra

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Nova Tabela**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um keyspace ou usando um existente.
   * Insira um nome de tabela.
   * Insira as propriedades e especifique uma chave primária.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

![Captura de tela da API do Cassandra, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Para a API do Cassandra, a chave primária é usada como a chave de partição.

### <a id="portal-gremlin"></a>API do Gremlin

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-graph-dotnet.md#create-a-database-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Novo Grafo**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID do grafo.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Insira uma chave de partição para vértices.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

![Captura de tela da API do Gremlin, caixa de diálogo Adicionar grafo](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>API de Tabela

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-table-dotnet.md#create-a-database-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Nova Tabela**. Em seguida, forneça os seguintes detalhes:

   * Insira uma ID da tabela.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

![Captura de tela da API de Tabela, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Para a API de Tabela, a chave de partição é especificada sempre que você adiciona uma nova linha.

## <a name="create-a-container-using-azure-cli"></a>Criar um contêiner usando a CLI do Azure

### <a id="cli-sql"></a>API do SQL

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>API do Azure Cosmos DB para MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>API do Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>API do Gremlin

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>API de Tabela

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-using-net-sdk"></a>Criar um contêiner usando o SDK do .NET

### <a id="dotnet-sql-graph"></a>API de SQL e API do Gremlin

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>API do Azure Cosmos DB para MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> O protocolo de transmissão do MongoDB não compreende o conceito de [Unidades de Solicitação](request-units.md). Para criar uma coleção contendo taxa de transferência provisionada, use o portal do Azure ou os SDKs do Cosmos DB para a API do SQL.

### <a id="dotnet-cassandra"></a>API do Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Próximas etapas

- [Particionamento no Azure Cosmos DB](partitioning-overview.md)
- [Unidades de Solicitação no Azure Cosmos DB](request-units.md)
- [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
- [Como trabalhar com a conta do Azure Cosmos](account-overview.md)
