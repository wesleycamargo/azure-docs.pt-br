---
title: Resolução de conflitos de vários mestres no Azure Cosmos DB
description: Este artigo descreve os modos de categorias de conflito e de resolução de conflitos como LWW (Last-Writer-Wins), Personalizado – Procedimento definido pelo usuário, Personalizado – Assíncrono nos vários mestres do Azure Cosmos DB.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 5feefdb8fe6204bc8ef42a5e65bf1e30354e0cf9
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393920"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Resolução de conflitos de vários mestres no Azure Cosmos DB 

Os vários mestres do Azure Cosmos DB fornecem modos de resolução global de conflitos para garantir que os dados sejam consistentes em todas as regiões em que são replicados.

## <a name="conflict-categories"></a>Categorias de conflito

Há três categorias de conflitos que podem ocorrer ao trabalhar usando dados do Azure Cosmos DB:

* **Conflitos de inserção:** esse tipo de conflito acontece quando o aplicativo insere dois ou mais documentos com o mesmo índice exclusivo (por exemplo, ID) de duas ou mais regiões simultaneamente. Nesse caso, todas as gravações podem ter êxito inicialmente, mas, com base na política de resolução de conflitos escolhida, apenas um documento com a ID original é confirmado.

* **Conflitos de substituição:** esse tipo de conflito acontece quando o aplicativo atualiza um único documento simultaneamente de duas ou mais regiões.

* **Conflitos de exclusão:** esse tipo de conflito acontece quando o aplicativo exclui um documento de uma região e o atualiza de uma ou mais regiões. 

## <a name="conflict-resolution-modes"></a>Modos de resolução de conflitos

Há três modos de resolução de conflitos oferecidos pelo Azure Cosmos DB. Os modos de resolução de conflitos são definidos para cada coleção.

> [!NOTE]
> Os usuários da API do SQL podem escolher entre três modos de resolução de conflitos diferentes. Para todos os outros modelos de API (MongoDB, Cassandra, Graph e Table), os conflitos são resolvidos usando Last-Writer-Wins.

### <a name="last-writer-wins-lww"></a>LWW (Last-Writer-Wins)

O Last-Writer-Wins é o modo de resolução de conflitos padrão. Nesse modo, os conflitos são resolvidos com base em um valor numérico passado em uma propriedade no documento.

O snippet de código a seguir é um exemplo de como configurar a política de resolução de conflitos Last-Writer-Wins ao usar o SDK do .Net. O "ConflictResolutionPath" define o caminho como a propriedade usada para resolver o conflito. Neste exemplo, `/userDefinedId` é o caminho de resolução de conflitos, e o documento com o maior valor `userDefinedId` sempre vencerá o conflito. Para registrar um modo de resolução Last-Writer-Wins, provisione a coleção com a ConflictResolutionPolicy conforme mostrado abaixo.

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 O modo de resolução Last-Writer-Wins é aplicado a diferentes categorias de conflitos de dados da seguinte maneira:

* **Conflitos de inserção e atualização:** se dois ou mais documentos entrarem em conflito em operações de inserção ou substituição, o documento que contém o maior valor para o caminho de resolução de conflitos se tornará o vencedor (ou seja, userDefinedId). Se vários documentos tiverem o mesmo valor numérico para o caminho de resolução de conflitos, o vencedor selecionado será não determinístico. No entanto, todas as regiões convergirão para um único vencedor.

* **Conflito de exclusão:** se houver conflitos de exclusão envolvidos, a exclusão sempre ganhará de outros conflitos de substituição, independentemente do valor do caminho de resolução de conflitos.

### <a name="custom--user-defined-procedure"></a>Personalizado – Procedimento definido pelo usuário

Nesse modo, o usuário controla a resolução de conflitos registrando um UDP (Procedimento definido pelo usuário) para a coleção. Este UDP tem uma assinatura específica. Se você selecionar esta opção, mas não registrar um UDP, ou se o UDP gerar uma exceção em tempo de execução, os conflitos serão escritos no feed de conflitos em que eles poderão ser resolvidos individualmente.

Para registrar um modo de resolução de conflitos de procedimento definido pelo usuário personalizado, provisione a coleção com o ConflictResolutionPolicy, conforme mostrado abaixo.

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

Em seguida, adicione o procedimento definido pelo usuário à coleção, conforme mostrado abaixo.

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

O procedimento armazenado registrado com a coleção tem uma assinatura especial. Neste exemplo abaixo, o UDP usa uma propriedade, `UserDefinedId` para resolver conflitos. O documento com o valor mais alto vence o conflito.

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

O procedimento tem quatro parâmetros:

* **incomingDocument:** especifica a versão conflitante do documento. O conflito pode ser de inserção, de substituição ou de exclusão. Para um conflito de exclusão, isso será uma imagem de exclusão (marca de exclusão) sem conteúdo.

* **existingDocument:** especifica a versão confirmada do documento, que tem o mesmo valor "rid" que o incomingDocument. Esse parâmetro é definido como nulo para um conflito de inserção e de exclusão.

* **isDeleteConflict:** sinalizador booliano indicando que o documento de entrada está conflitando com um documento excluído anteriormente. Quando esse parâmetro é definido como true, existingDocument também será nulo.

* **conflictingDocuments:** especifica uma coleção da versão confirmada de todos os documentos no banco de dados, que estão conflitando com incomingDocument na coluna de ID ou quaisquer campos de índice exclusivo. Esses documentos terão um valor "rid" diferente quando comparados com o incomingDocument.

O procedimento definido pelo usuário tem acesso completo à chave de partição do Cosmos DB e pode executar qualquer operação de armazenamento para resolver conflitos. Se o procedimento definido pelo usuário não confirmar a versão do conflito, o sistema removerá o conflito e o existingDocument permanecerá confirmado. Se o procedimento definido pelo usuário falhar ou não existir, o Azure Cosmos DB adicionará o conflito no feed de conflitos somente leitura em que ele poderá ser processado de maneira assíncrona, conforme mostrado no [Modo de resolução de conflitos assíncrono](). 

### <a name="custom--asynchronous"></a>Personalizado – Assíncrono  

Nesse modo, o Azure Cosmos DB exclui todos os conflitos (de inserção, substituição e exclusão) da confirmação e registra-os no feed de conflitos somente leitura para resolução adiada pelo aplicativo do usuário. O aplicativo pode executar a resolução de conflitos de forma assíncrona e usar qualquer lógica ou referenciar qualquer fonte externa, aplicativo ou serviço para resolver o conflito.

Para registrar um modo de resolução de conflitos assíncrono personalizado, provisione a coleção com o ConflictResolutionPolicy, conforme mostrado abaixo.

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

Para ler e processar quaisquer conflitos no feed de conflitos, implemente o código mostrado abaixo. Os dados armazenados no feed de conflitos adicionam custos de armazenamento. Portanto, recomenda-se excluir os dados armazenados no feed de conflitos depois que eles são processados.

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> O feed de conflitos não inclui um ouvinte para enviar notificações para o processamento de downstream, como o feed de alterações no Cosmos DB. É necessário implementar a lógica para sondar o feed de conflitos e determinar se eles estão presentes.

## <a name="code-samples"></a>Exemplos de código

Abaixo estão os aplicativos de exemplo que demonstram a resolução de conflitos para as APIs listadas. Cada exemplo gera conflitos dentro de um contêiner e, em seguida, demonstra como os conflitos são resolvidos para cada modo de resolução de conflitos com suporte.

|Modelo de API  | . |Amostra |
|---------|---------|---------|
|API do SQL    | .NET    |[azure-cosmos-db-sql-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|API do SQL    | Nó    |[azure-cosmos-js/samples/MultiRegionWrite/](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|API do SQL    | Java    |[azure-cosmos-db-sql-java-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |
|MongoDB  | .NET    |[azure-cosmos-db-mongodb-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|API de Tabela  | .NET    |[azure-cosmos-db-table-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|API do Gremlin | .NET | [azure-cosmos-db-gremlin-dontnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-gremlin-dontnet-multi-master)|

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você soube mais sobre categorias de conflito e modos de resolução de conflitos para o Azure Cosmos DB. Em seguida, dê uma olhada nos seguintes recursos:

* [Usar clientes do MongoDB com vários mestres](multi-master-oss-nosql.md)
