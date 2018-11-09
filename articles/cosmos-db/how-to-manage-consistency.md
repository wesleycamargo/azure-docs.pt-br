---
title: Saiba como gerenciar a consistência no Azure Cosmos DB
description: Saiba como gerenciar a consistência no Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: aa42737274432180540584c01a7125c1a9765b49
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243649"
---
# <a name="manage-consistency"></a>Gerenciar consistência

Este artigo explica as diferentes maneiras de definir a consistência padrão, substitui essa consistência no cliente, gerencia tokens de sessão manualmente e explica a métrica PBS (Desatualização Limitada Probabilística).

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência padrão

O nível de consistência padrão é o nível de consistência que os clientes usarão por padrão. Ele pode ser substituído pelos clientes.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

Este exemplo a seguir cria uma nova conta do Cosmos DB com vários mestres habilitada nas regiões Leste dos EUA e Oeste dos EUA, configurando a política de consistência padrão como Desatualização Limitada com intervalo de desatualização máximo de 10 segundos e número máximo de solicitações de desatualização toleradas em 200.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

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

Para exibir ou modificar o nível de consistência padrão, entre no portal do Azure. Localize a conta do Cosmos DB e abra o painel **Consistência padrão**. A partir daí, escolha o nível de consistência que você gostaria de ter como o novo padrão e clique em Salvar.

![Imagem do menu de consistência no portal do Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Substituir o nível de consistência padrão

Os clientes podem substituir o nível de consistência padrão que é definido pelo serviço. Isso pode ser feito para todos os clientes ou por solicitação.

### <a id="override-default-consistency-dotnet"></a>.NET

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

### <a id="override-default-consistency-java-async"></a>Java Assíncrono

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

### <a id="override-default-consistency-java-sync"></a>Java Síncrono

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Utilizar tokens de sessão

Se você quiser gerenciar os tokens de sessão manualmente, poderá obtê-los das respostas e defini-los por solicitação. Se você não tiver a necessidade de gerenciar manualmente os tokens de sessão, não precisar usar os exemplos abaixo. O SDK controlará automaticamente os tokens de sessão e usará o token de sessão mais recente se você não defini-lo por conta própria.

### <a id="utilize-session-tokens-dotnet"></a>.NET

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Assíncrono

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

### <a id="utilize-session-tokens-java-sync"></a>Java Síncrono

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python

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

Para exibir a métrica PBS, vá para a conta do Cosmos DB no portal do Azure e abra o painel **Métrica**. A partir daí, clique na guia **Consistência** guia e examine o gráfico chamado "**Probabilidade de leituras altamente consistentes com base em sua carga de trabalho (confira PBS)**".

![Imagem do gráfico PBS no portal do Azure](./media/how-to-manage-consistency/pbs-metric.png)

Você deve usar o menu de métrica do Cosmos DB para ver essa métrica. Ele não aparecerá na experiência de métrica de Monitoramento do Azure.

## <a name="next-steps"></a>Próximas etapas

Você pode aprender mais sobre como gerenciar conflitos de dados ou passar para o próximo conceito fundamental no Cosmos DB usando os seguintes documentos:

* [Como gerenciar conflitos entre regiões](how-to-manage-conflicts.md)
* [Particionamento e distribuição de dados](partition-data.md)
