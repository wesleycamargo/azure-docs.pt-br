---
title: Criar um contêiner no Azure Cosmos DB
description: Saiba como criar um contêiner no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 3952ad98cf734de322741ace80c17029e282b94c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068707"
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
# Create a container with a partition key and provision 400 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

### <a id="cli-mongodb"></a>API do Azure Cosmos DB para MongoDB

```azurecli-interactive
# Create a collection with a shard key and provision 400 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 400
```

### <a id="cli-cassandra"></a>API do Cassandra

```azurecli-interactive
# Create a table with a partition/primary key and provision 400 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 400
```

### <a id="cli-gremlin"></a>API do Gremlin

```azurecli-interactive
# Create a graph with a partition key and provision 400 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

### <a id="cli-table"></a>API de Tabela

```azurecli-interactive
# Create a table with 400 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 400
```

## <a name="create-a-container-using-powershell"></a>Criar um contêiner usando o PowerShell

Os exemplos a seguir mostram como criar todos os recursos de suporte necessários para provisionar um recurso de nível de contêiner no Azure Cosmos DB

### <a id="ps-sql"></a>API do SQL

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$databaseName = "database1"
$databaseResourceName = $accountName + "/sql/" + $databaseName
$containerName = "container1"
$containerResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

# Create account
$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$accountProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "GlobalDocumentDB" -Name $accountName -PropertyObject $accountProperties


# Create database with shared throughput
$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties

# Create a container with default policies
$containerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerResourceName -PropertyObject $containerProperties 
```

### <a id="ps-cassandra"></a>API do Cassandra

```azurepowershell-interactive
# Create an Azure Cosmos Account for Cassandra API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$keyspaceName = "keyspace1"
$keyspaceResourceName = $accountName + "/cassandra/" + $keyspaceName
$tableName = "table1"
$tableResourceName = $accountName + "/cassandra/" + $keyspaceName + "/" + $tableName

# Create account
$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$accountProperties = @{
    "capabilities"= @( @{ "name"="EnableCassandra" } );
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "GlobalDocumentDB" -Name $accountName -PropertyObject $accountProperties

# Create keyspace with shared throughput
$keyspaceProperties = @{
    "resource"=@{ "id"=$keyspaceName };
    "options"=@{ "Throughput"="400" }
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/keyspaces" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $keyspaceResourceName -PropertyObject $keyspaceProperties

# Create a table
$tableProperties = @{
    "resource"=@{
        "id"=$tableName; 
        "schema"= @{
            "columns"= @(
                @{ "name"= "loadid"; "type"= "uuid" };
                @{ "name"= "machine"; "type"= "uuid" };
                @{ "name"= "cpu"; "type"= "int" };
                @{ "name"= "mtime"; "type"= "int" };
                @{ "name"= "load"; "type"= "float" };
            );
            "partitionKeys"= @(
                @{ "name"= "machine" };
                @{ "name"= "cpu" };
                @{ "name"= "mtime" }; 
            );
            "clusterKeys"= @( 
                @{ "name"= "loadid"; "orderBy"= "asc" }
            )
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/keyspaces/tables" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $tableResourceName -PropertyObject $tableProperties 

```

### <a id="ps-mongodb"></a>API do MongoDB

```azurepowershell-interactive
# Create a collection for an Azure Cosmos Account for MongoDB API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$databaseName = "database1"
$databaseResourceName = $accountName + "/mongodb/" + $databaseName
$collectionName = "collection1"
$collectionResourceName = $accountName + "/mongodb/" + $databaseName + "/" + $collectionName

# Create account
$locations = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="North Central US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$accountProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "MongoDB" -Name $accountName -PropertyObject $accountProperties


# Create database with shared throughput
$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties


# Create Collection
$collectionProperties = @{
    "resource"=@{
        "id"=$collectionName; 
        "shardKey"= @{ "user_id"="Hash" };
        "indexes"= @(
            @{
                "key"= @{ "keys"=@("user_id", "user_address") };
                "options"= @{ "unique"= "true" }
            };
            @{
                "key"= @{ "keys"=@("_ts") };
                "options"= @{ "expireAfterSeconds"= "1000" }
            }
        )
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/collections" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $collectionResourceName -PropertyObject $collectionProperties 
```

### <a id="ps-gremlin"></a>API do Gremlin

```azurepowershell-interactive
# Create an Azure Cosmos Account for Gremlin API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$databaseName = "database1"
$databaseResourceName = $accountName + "/gremlin/" + $databaseName
$graphName = "graph1"
$graphResourceName = $accountName + "/gremlin/" + $databaseName + "/" + $graphName

# Create account
$locations = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="North Central US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$accountProperties = @{
    "capabilities"= @( @{ "name"="EnableGremlin" } );
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "GlobalDocumentDB" -Name $accountName -PropertyObject $accountProperties


# Create database with shared throughput
$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties


# Create a graph with defaults
$graphProperties = @{
    "resource"=@{
        "id"=$graphName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/graphs" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $graphResourceName -PropertyObject $graphProperties 
```

### <a id="ps-table"></a>API de Tabela

```azurepowershell-interactive
# Create an Azure Cosmos account for Table API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.
$tableName = "table1"
$tableResourceName = $accountName + "/table/" + $tableName

# Create account
$locations = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="North Central US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$accountProperties = @{
    "capabilities"= @( @{ "name"="EnableTable" } );
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Kind "GlobalDocumentDB" -Name $accountName -PropertyObject $accountProperties


# Create table
$tableProperties = @{
    "resource"=@{ "id"=$tableName };
    "options"=@{ "Throughput"="400" }
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/tables" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $tableResourceName -PropertyObject $tableProperties
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