---
title: Carregar dados para um índice do Azure Search em C# (SDK do .NET) - Azure Search
description: Saiba como carregar dados em um índice de texto completo pesquisável no Azure Search usando código de exemplo C# e o SDK do .NET.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286900"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>Início rápido: 2 - Carregar dados para um índice do Azure Search usando C#

Este artigo mostra como importar dados para [um índice do Azure Search](search-what-is-an-index.md) usando C# e o [SDK do .NET](https://aka.ms/search-sdk). Efetuar push de documentos para o índice é realizado executando estas tarefas:

> [!div class="checklist"]
> * Crie um objeto [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) para conectar um índice de pesquisa.
> * Crie um objeto [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) contendo os documentos a serem adicionados, modificados ou excluídos.
> * Chame o método `Documents.Index` no `SearchIndexClient` para carregar documentos para um índice.

## <a name="prerequisites"></a>Pré-requisitos

[Crie um índice do Azure Search](search-create-index-dotnet.md) e um objeto `SearchServiceClient`, conforme mostrado em ["Criar um cliente"](search-create-index-dotnet.md#CreateSearchServiceClient).


## <a name="create-a-client"></a>Criar um cliente
Para importar dados, é necessário ter uma instância da classe `SearchIndexClient`. Há várias abordagens para criar essa classe, incluindo o uso da instância `SearchServiceClient` que já foi criada. 

Como o exemplo a seguir ilustra, é possível usar a instância `SearchServiceClient`e chamar o método `Indexes.GetClient`. Esse snippet de código obtém um `SearchIndexClient` para o índice nomeado "hotéis" de um `SearchServiceClient` nomeado `serviceClient`.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` tem uma propriedade `Documents`. Esta propriedade fornece todos os métodos que você precisa para adicionar, modificar, excluir ou consultar documentos no índice.

> [!NOTE]
> Em um aplicativo de pesquisa típico, a consulta e a indexação são tratadas separadamente. Embora `Indexes.GetClient` seja conveniente porque é possível reutilizar objetos como `SearchCredentials`, uma abordagem mais robusta envolve a criação de `SearchIndexClient` diretamente para que você possa passar uma chave de consulta, em vez de uma chave de administração. Essa prática é consistente com o [princípio de privilégios mínimos](https://en.wikipedia.org/wiki/Principle_of_least_privilege) e ajuda a tornar o aplicativo mais seguro. Você construirá um `SearchIndexClient` no próximo exercício. Para obter mais informações sobre chaves, consulte [Criar e gerenciar chaves de API para um serviço Azure Search](search-security-api-keys.md).
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>Construir IndexBatch

Para importar dados usando o SDK do .NET, empacote os dados em um objeto `IndexBatch`. Um `IndexBatch` encapsula uma coleção de objetos `IndexAction`, cada um contendo um documento e uma propriedade que informa ao Azure Search qual ação executar nesse documento (carregar, mesclar, excluir e mergeOrUpload). Para obter mais informações sobre ações de indexação, consulte [Ações de indexação: carregar, mesclar, mergeOrUpload, excluir](search-what-is-data-import.md#indexing-actions).

Assumindo que você sabe quais ações executar nos documentos, você está pronto para construir o `IndexBatch`. O exemplo a seguir mostra como criar um lote com algumas ações diferentes. O exemplo usa uma classe personalizada chamada `Hotel` que mapeia para um documento no índice "hotéis".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Nesse caso, estamos usando `Upload`, `MergeOrUpload` e `Delete` como nossas ações de pesquisa, conforme especificado pelos métodos chamados na classe `IndexAction`.

Suponha que o índice de exemplo "hotels" já esteja preenchido com vários documentos. Observe como não precisamos especificar todos os campos de documento possíveis ao usar `MergeOrUpload` e como especificamos apenas a chave do documento (`HotelId`) ao usar `Delete`.

Além disso, observe que você só pode incluir até 1000 documentos em uma única solicitação de indexação.

> [!NOTE]
> Neste exemplo, estamos aplicando ações diferentes para documentos diferentes. Se você quisesse executar as mesmas ações em todos os documentos do lote, em vez de chamar `IndexBatch.New`, poderia usar os outros métodos estáticos de `IndexBatch`. Por exemplo, você poderia criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete`. Esses métodos usam um conjunto de documentos (objetos do tipo `Hotel` neste exemplo) em vez de objetos `IndexAction`.
> 
> 

## <a name="call-documentsindex"></a>Chamar Documents.Index
Agora que você tem um objeto `IndexBatch` inicializado, pode enviá-lo para o índice chamando `Documents.Index` em seu objeto `SearchIndexClient`. O exemplo a seguir mostra como chamar `Index`, assim como algumas etapas adicionais que você precisa executar:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Observe `try`/`catch` em torno da chamada para o método `Index`. O bloco catch lida com um caso de erro importante para a indexação. Se o serviço de Azure Search não indexar alguns documentos no lote, uma `IndexBatchException` será lançada por `Documents.Index`. Isso pode acontecer se você estiver indexando documentos enquanto o serviço estiver sob carga pesada. **É altamente recomendável a manipulação explícita desse caso em seu código.**  Você pode atrasar e repetir a indexação de documentos que falharam, ou você pode registrar em log e continuar, como faz o exemplo, ou pode alguma outra coisa, dependendo dos requisitos de consistência de dados do aplicativo.

Por fim, o código no exemplo acima, atrasa por dois segundos. A indexação ocorre de maneira assíncrona em seu serviço de Azure Search, portanto, o exemplo de aplicativo precisa aguardar alguns instantes para garantir que os documentos estejam disponíveis para pesquisa. Normalmente, atrasos como esses só são necessários em demonstrações, testes e exemplos de aplicativos.

Para obter mais informações sobre o processamento de documentos, consulte ["Como o SDK do .NET trata documentos"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).


## <a name="next-steps"></a>Próximas etapas
Após preencher o índice do Azure Search, a próxima etapa será a emissão de consultas para pesquisar documentos. 

> [!div class="nextstepaction"]
> [Consultar um índice do Azure Search em C#](search-query-dotnet.md)
