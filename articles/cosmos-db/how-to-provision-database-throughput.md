---
title: Provisionar taxa de transferência de banco de dados no Azure Cosmos DB
description: Saiba como provisionar a taxa de transferência no nível de banco de dados no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 7c253141e0e6e76f845d08e68a1d79949fe811e8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034226"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Provisionar taxa de transferência de um banco de dados no Azure Cosmos DB

Este artigo explica como provisionar a taxa de transferência de um banco de dados no Azure Cosmos DB. Você pode provisionar a taxa de transferência para um único [contêiner](how-to-provision-container-throughput.md) ou para um banco de dados e compartilhá-la entre os contêineres dentro dele. Você pode provisionar a taxa de transferência no nível de banco de dados usando o portal do Azure ou os SDKs do Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Provisionar a taxa de transferência usando o portal do Azure

### <a id="portal-sql"></a>API de SQL (Core)

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Criar uma nova conta do Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) ou selecionar uma conta existente.

1. Abra o painel **Data Explorer** e selecione **Novo banco de dados**. Em seguida, preencha o formulário com os seguintes detalhes:

   * Insera uma ID do banco de dados. 
   * Selecione Provisionar taxa de transferência.
   * Insira uma taxa de transferência, por exemplo, 1000 RUs.
   * Selecione **OK**.

![Aprovisionar a taxa de transferência de bancos de dados usando a API SQL](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Provisionar a taxa de transferência usando o SDK .NET

> [!Note]
> Use a API de SQL para provisionar a taxa de transferência de todas as APIs. Opcionalmente, você pode usar o exemplo a seguir para a API do Cassandra também.

### <a id="dotnet-all"></a>Todas as APIs

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>API do Cassandra

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para saber mais sobre o provisionamento de taxa de transferência no Cosmos DB:

* [Como provisionar a taxa de transferência para um contêiner](how-to-provision-container-throughput.md)
* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
