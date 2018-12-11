---
title: Saiba como gerenciar a consistência no Azure Cosmos DB
description: Saiba como gerenciar a consistência no Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 9d8c1296fc811d97dc9e7e66ad9bd9fdc79d66f9
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634329"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gerenciar os níveis de coerência no Azure Cosmos DB

Este artigo explica como gerenciar os níveis de consistência no Azure Cosmos DB. Você aprende como configurar o nível de consistência padrão, substituir a consistência padrão, gerenciar manualmente os tokens de sessão e compreender a métrica PBS (Desatualização Limitada Probabilística).

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência padrão

O nível de consistência padrão é o nível de coerência que os clientes usam por padrão. Os clientes podem substituí-lo.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

Este exemplo cria uma nova conta do Azure Cosmos DB com vários mestres habilitados nas regiões Leste dos EUA e Oeste dos EUA. A política de consistência padrão é definida como Sessão.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>Portal

Para exibir ou modificar o nível de consistência padrão, entre no portal do Azure. Localize a conta do Azure Cosmos DB e abra o painel **Consistência Padrão**. Escolha o nível de consistência que você gostaria de ter como o novo padrão e escolha **Salvar**.

![Menu de consistência no portal do Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Substituir o nível de consistência padrão

Os clientes podem substituir o nível de consistência padrão que é definido pelo serviço. Essa opção pode ser definida para todos os clientes ou por solicitação.

### <a id="override-default-consistency-dotnet"></a>SDK .NET

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>SDK de Java Assíncrono

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>SDK de Java Síncrono

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>SDK do Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Utilizar tokens de sessão

Para gerenciar os tokens de sessão manualmente, obtenha o token de sessão na resposta e configure-os por solicitação. Se não for necessário gerenciar manualmente os tokens de sessão, você não precisa usar esses exemplos. O SDK controla os tokens de sessão automaticamente. Se você não definir o token de sessão manualmente, por padrão, o SDK usará o token de sessão mais recente.

### <a id="utilize-session-tokens-dotnet"></a>SDK .NET

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>SDK de Java Assíncrono

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>SDK de Java Síncrono

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>SDK do Python

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Monitorar métrica PBS (Desatualização Limitada Probabilística)

Para exibir a métrica PBS, vá para a conta do Azure Cosmos DB no portal do Azure. Abra o painel **Métricas** e escolha a guia **Consistência**. Examine o gráfico chamado **Probabilidade de leituras altamente consistentes com base em sua carga de trabalho (confira PBS)**.

![Gráfico PBS no portal do Azure](./media/how-to-manage-consistency/pbs-metric.png)

Use o menu de métricas do Azure Cosmos DB para ver essa métrica. Ele não será exibida na experiência de métrica de Monitoramento do Azure.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como gerenciar conflitos de dados ou passar para o próximo conceito fundamental no Azure Cosmos DB. Confira os seguintes artigos:

* [Gerenciar conflitos entre regiões](how-to-manage-conflicts.md)
* [Particionamento e distribuição de dados](partition-data.md)
