---
title: Provisionar taxa de transferência de banco de dados no Azure Cosmos DB
description: Saiba como provisionar a taxa de transferência no nível de banco de dados no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: da56c06e215e02ee3eefe3d0552c962a8c59011e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683470"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Provisionar taxa de transferência em um banco de dados no Azure Cosmos DB

Este artigo explica como provisionar a taxa de transferência em um banco de dados no Azure Cosmos DB. Você pode provisionar a taxa de transferência para um único [contêiner](how-to-provision-container-throughput.md) ou para um banco de dados e compartilhá-la entre os contêineres dentro dele. Para saber quando usar taxa de transferência no nível de banco de dados e no nível de contêiner, consulte o artigo [Casos de uso para provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md). É possível provisionar a taxa de transferência no nível do banco de dados usando o portal do Azure ou os SDKs do Azure Cosmos DB.

## <a name="provision-throughput-using-azure-portal"></a>Provisionar a taxa de transferência usando o portal do Azure

### <a id="portal-sql"></a>API de SQL (Core)

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Novo Banco de Dados**. Forneça os seguintes detalhes:

   * Insira uma ID do banco de dados. 
   * Selecione **Provisionar taxa de transferência**.
   * Insira uma taxa de transferência (por exemplo, 1000 RUs).
   * Selecione **OK**.

![Captura de tela da caixa de diálogo Novo Banco de Dados](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Provisionar a taxa de transferência usando o SDK .NET

> [!Note]
> Você pode usar os SDKs do Cosmos para a API de SQL para provisionar a taxa de transferência de todas as APIs. Opcionalmente, você pode usar o exemplo a seguir para a API do Cassandra também.

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

Consulte os seguintes artigos para saber mais sobre taxa de transferência provisionada no Azure Cosmos DB:

* [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como provisionar a taxa de transferência para um contêiner](how-to-provision-container-throughput.md)
* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)