---
title: Saiba como gerenciar conflitos entre regiões no Azure Cosmos DB
description: Saiba como gerenciar conflitos no Azure Cosmos DB
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 52bcedace5d8a039be4a72dcfcc3cdeb653b3e0c
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354358"
---
# <a name="manage-conflicts-between-regions"></a>Gerenciar conflitos entre regiões

Com gravações em várias regiões, quando ocorre um conflito de dados, você pode resolver o conflito usando políticas de resolução de conflitos diferentes. Este artigo descreve como gerenciar políticas de resolução de conflitos usando plataformas em idiomas diferentes.

## <a name="create-a-custom-conflict-resolution-policy"></a>Criar uma política de resolução de conflitos personalizada

Estes exemplos mostram como configurar um contêiner com uma política de resolução de conflitos personalizada. Esses conflitos aparecem no feed de conflitos.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>SDK .NET

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-java-async"></a>SDK de Java Assíncrono

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>SDK de Java Síncrono

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a id="create-custom-conflict-resolution-policy-python"></a>SDK do Python

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
                    'id': self.manual_collection_name,
                    'conflictResolutionPolicy': {
                          'mode': 'Custom'
                        }
                    }
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="create-a-custom-conflict-resolution-policy-with-a-stored-procedure"></a>Criar uma política de resolução de conflitos personalizada com um procedimento armazenado

Estes exemplos mostram como configurar um contêiner com uma política de resolução de conflitos personalizada com procedimento armazenado para resolver o conflito. Esses conflitos não aparecem no feed de conflitos se não houver um erro em seu procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>SDK .NET

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });
```

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>SDK de Java Assíncrono

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>SDK de Java Síncrono

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>SDK do Python

```python

```

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Crie uma política de resolução de conflitos do tipo “última gravação ganha”

Estes exemplos mostram como configurar um contêiner com uma política de resolução de conflitos do tipo “última gravação ganha”. Se o caminho não estiver definido ou for inválido, o padrão será a propriedade `_ts`. Essa propriedade é o campo de carimbo de data/hora. Esses conflitos não aparecem no feed de conflitos.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>SDK .NET

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/regionId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>SDK de Java Assíncrono

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy(conflictResolutionPath);
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>SDK de Java Síncrono

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/regionId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/regionId"
    }
  }
);
```

Se você deixar a propriedade `conflictResolutionPath`, ele assume como padrão a propriedade `_ts`.

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>SDK do Python

```python
udp_collection = {
                'id': self.udp_collection_name,
                'conflictResolutionPolicy': {
                    'mode': 'Custom',
                    'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
                    }
                }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

## <a name="read-from-conflict-feed"></a>Ler o feed de conflitos

Estes exemplos mostram como ler o feed de conflitos do contêiner. Os conflitos só aparecem no feed de conflitos se não forem resolvidos automaticamente.

### <a id="read-from-conflict-feed-dotnet"></a>SDK .NET

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-java-async"></a>SDK de Java Assíncrono

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>SDK de Java Síncrono

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os conceitos do Azure Cosmos DB a seguir:

* [Particionamento e distribuição de dados](partition-data.md)
* [Indexação no Azure Cosmos DB](indexing-policies.md)

