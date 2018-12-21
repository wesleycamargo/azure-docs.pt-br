---
title: Provisionar taxa de transferência de contêiner no Azure Cosmos DB
description: Saiba como provisionar a taxa de transferência no nível do contêiner no Azure Cosmos DB
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: dd47976bca75569142f1912eee06c66061e92fa6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097658"
---
# <a name="provision-throughput-for-an-azure-cosmos-db-container"></a>Provisionar taxa de transferência de um contêiner do Azure Cosmos DB

Este artigo explica como provisionar a taxa de transferência de um contêiner (coleção, gráfico, tabela) no Azure Cosmos DB. Você pode provisionar a taxa de transferência para um único contêiner ou [provisionar para um banco de dados](how-to-provision-database-throughput.md) e compartilhá-la entre os contêineres no banco de dados. Você pode provisionar a taxa de transferência para um contêiner usando o portal do Azure, a CLI do Azure ou SDKs do Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Provisionar a taxa de transferência usando o portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) ou selecionar uma conta existente.

1. Abra o painel **Data Explorer** e selecione **Nova Coleção**. Em seguida, preencha o formulário com os seguintes detalhes:

   * Crie um novo banco de dados ou use um existente.
   * Insira uma ID de coleção (ou tabela, grafo).
   * Insira um valor de chave de partição, por exemplo `/userid`.
   * Insira uma taxa de transferência, por exemplo, 1000 RUs.
   * Selecione **OK**.

![Provisionar a taxa de transferência de contêiner da API de SQL](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Provisionar a taxa de transferência usando a CLI do Azure

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

Se você estiver provisionando a taxa de transferência para a conta da API do MongoDB, use '/myShardKey' como o caminho da chave de partição e, ao provisionar a taxa de transferência para a conta da API do Cassandra, use '/myPrimaryKey' como o caminho da chave de partição.

## <a name="provision-throughput-using-net-sdk"></a>Provisionar a taxa de transferência usando o SDK .NET

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

Consulte os seguintes artigos para saber mais sobre o provisionamento de taxa de transferência no Cosmos DB:

* [Como provisionar a taxa de transferência para um banco de dados](how-to-provision-database-throughput.md)
* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
