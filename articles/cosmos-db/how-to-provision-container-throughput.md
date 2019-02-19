---
title: Provisionar taxa de transferência de contêiner no Azure Cosmos DB
description: Saiba como provisionar a taxa de transferência no nível do contêiner no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 4df8a12581b5d71a76964ca1e3d40c6c53185f67
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860313"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Provisionar a taxa de transferência em um contêiner do Azure Cosmos

Este artigo explica como provisionar a taxa de transferência de um contêiner (coleção, grafo, tabela) no Azure Cosmos DB. É possível provisionar a taxa de transferência para um único contêiner ou [provisionar para um banco de dados](how-to-provision-database-throughput.md) e compartilhá-la entre os contêineres no banco de dados. É possível provisionar a taxa de transferência para um contêiner usando o portal do Azure, a CLI do Azure ou SDKs do Azure Cosmos DB.

## <a name="provision-throughput-by-using-azure-portal"></a>Provisionar a produtividade usando o portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) ou selecione uma conta existente.

1. Abra o painel **Data Explorer** e selecione **Nova Coleção**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID de coleção (ou tabela ou grafo).
   * Insira um valor de chave de partição (por exemplo, `/userid`).
   * Insira uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de tela do Data Explorer, com Nova coleção realçado](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-azure-cli"></a>Provisionar a taxa de transferência usando a CLI do Azure

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

Se você estiver provisionando a taxa de transferência para uma conta do Azure Cosmos DB configurada com a API do Azure Cosmos DB para MongoDB, use `/myShardKey` para o caminho da chave de partição. Se você estiver provisionando a taxa de transferência para uma conta do Azure Cosmos DB configurada para a API do Cassandra, use `/myPrimaryKey` para o caminho da chave de partição.

## <a name="provision-throughput-by-using-net-sdk"></a>Provisionar taxa de transferência usando o SDK do .NET

> [!Note]
> Use a API de SQL para provisionar a taxa de transferência de todas as APIs, exceto da API do Cassandra.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin e APIs de Tabela

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>API do Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre o provisionamento de taxa de transferência no Azure Cosmos DB:

* [Como provisionar a taxa de transferência para um banco de dados](how-to-provision-database-throughput.md)
* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
