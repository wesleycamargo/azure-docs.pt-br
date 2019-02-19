---
title: Como configurar vários mestres no Azure Cosmos DB
description: Saiba como configurar vários mestres nos aplicativos do Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: effe6fa942ce0cabace08e72dba90baf8646680e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118782"
---
# <a name="how-to-configure-multi-master-in-your-applications-in-azure-cosmos-db"></a>Como configurar vários mestres nos aplicativos do Azure Cosmos DB

Para usar recursos de vários mestres nos aplicativos, você precisa habilitar gravações de várias regiões no aplicativo e configurar a funcionalidade de hospedagem múltipla (multihoming) definindo a região atual na qual o aplicativo é implantado.

## <a id="netv2"></a>SDK do .NET v2

Para habilitar vários mestres nos aplicativos, defina `UseMultipleWriteLocations` como verdadeiro e configure `SetCurrentLocation` para a região na qual o aplicativo está sendo implantado e o Cosmos DB é replicado.

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

Para habilitar vários mestres nos aplicativos, defina `policy.setUsingMultipleWriteLocations(true)` como verdadeiro e configure `policy.setPreferredLocations` para a região na qual o aplicativo está sendo implantado e o Cosmos DB é replicado.

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

Saiba mais sobre os vários mestres, a distribuição global e a consistência no Azure Cosmos DB. Confira os seguintes artigos:

* [Utilizar tokens de sessão para gerenciar a consistência no Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)

* [Tipos de conflitos e políticas de resolução no Azure Cosmos DB](conflict-resolution-policies.md)

* [Alta disponibilidade no Azure Cosmos DB](high-availability.md)

* [Escolhendo o nível de consistência correto no Azure Cosmos DB](consistency-levels-choosing.md)

* [Compensações de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)
