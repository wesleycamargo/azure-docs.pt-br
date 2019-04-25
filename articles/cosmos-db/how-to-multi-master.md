---
title: Como configurar vários mestres no Azure Cosmos DB
description: Saiba como configurar vários mestres nos aplicativos do Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: b862c59002369662d37b6d6a9de28370b0000497
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682263"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Como configurar vários mestres nos aplicativos que usam o Azure Cosmos DB

Para usar recursos de vários mestres em seu aplicativo, você precisará habilitar gravações em várias regiões e configurar a capacidade de hospedagem múltipla (multihoming) no Azure Cosmos DB. A hospedagem múltipla é configurada com a definição da região na qual o aplicativo é implantado.

## <a id="netv2"></a>SDK do .NET v2

Para habilitar vários mestres nos aplicativos, defina `UseMultipleWriteLocations` como verdadeiro e configure `SetCurrentLocation` para a região na qual o aplicativo está sendo implantado e o Azure Cosmos DB é replicado.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>SDK do .NET v3 (versão prévia)

Para habilitar vários mestres nos aplicativos, configure `UseCurrentRegion` para a região na qual o aplicativo está sendo implantado e o Cosmos DB é replicado.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>SDK de Java Assíncrono

Para habilitar vários mestres nos aplicativos, defina `policy.setUsingMultipleWriteLocations(true)` e configure `policy.setPreferredLocations` para a região na qual o aplicativo está sendo implantado e o Cosmos DB é replicado.

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>SDK do Node.js, SDK do JavaScript e TypeScript SDK

Para habilitar vários mestres nos aplicativos, defina `connectionPolicy.UseMultipleWriteLocations` como verdadeiro e configure `connectionPolicy.PreferredLocations` para a região na qual o aplicativo está sendo implantado e o Cosmos DB é replicado.

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>SDK do Python

Para habilitar vários mestres nos aplicativos, defina `connection_policy.UseMultipleWriteLocations` como verdadeiro e configure `connection_policy.PreferredLocations` para a região na qual o aplicativo está sendo implantado e o Cosmos DB é replicado.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Próximas etapas

Em seguida, você poderá ler os artigos a seguir:

* [Utilizar tokens de sessão para gerenciar a consistência no Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipos de conflitos e políticas de resolução no Azure Cosmos DB](conflict-resolution-policies.md)
* [Alta disponibilidade no Azure Cosmos DB](high-availability.md)
* [Níveis de coerência no Azure Cosmos DB](consistency-levels.md)
* [Escolhendo o nível de consistência correto no Azure Cosmos DB](consistency-levels-choosing.md)
* [Compensações de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
