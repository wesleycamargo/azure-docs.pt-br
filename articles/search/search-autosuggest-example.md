---
title: Exemplo de como adicionar termos de lista suspensa para uma caixa de pesquisa - Azure Search de sugestão automática
description: Adicione entradas de consulta sugeridos criando sugestores e formular solicitações que invocam as consultas sugeridas no Azure Search.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373069"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>Exemplo: Adicionar para seleções da consulta de lista suspensa de sugestão automática

Entradas de termo de pesquisa podem incluir uma lista suspensa dos termos de consulta sugeridos. Você já viu essa funcionalidade nos mecanismos de pesquisa comerciais, e você pode implementar uma experiência semelhante no Azure Search usando um [sugestor constructo](index-add-suggesters.md) e uma operação de sugestões em uma solicitação de consulta. Este artigo usa exemplos para demonstrar a formulação de uma consulta de sugestão automática, usando um sugestor que você já definiu. 

## <a name="rest-api"></a>API REST

Você pode usar [Postman](search-fiddler.md) ou [PowerShell](search-create-index-rest-api.md) e o [API REST](https://docs.microsoft.com/rest/api/searchservice/) testar este exemplo, mas os resultados serão retornados como documentos JSON. Uma experiência mais realista e visual pode ser encontrada usando o [exemplos de código para preenchimento automático](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

### <a name="1---index-with-a-suggester-construct"></a>1 - o índice com uma construção de sugestor

Sugestão automática começa com um [sugestor constructo](index-add-suggesters.md) adicionados a um índice composto de campos que contribuem com valores de lista suspensa. Dado o esquema a seguir, as consultas sugeridas serão ser formuladas usando os valores dos campos hotelName e categoria.

Supondo que os conjuntos de dados de exemplo mínimo encontrados nos guias de início rápido, nomes de hotel incluem "Permanecer sofisticada" e "Roach motel" e as categorias incluem "Luxury" e "Budget".

Se você já tiver o índice de hotéis, você deve descartar e recriá-la usando o esquema a seguir. Esse esquema adiciona um sugestor. Lembre-se de recarregar os dados também.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2 - consultar com o operador de sugestões

Para usar um sugestor e invocar sugestão automática, você deve enviar uma [API de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) solicitar usando GET ou POST. Na solicitação, o serviço de pesquisa procura correspondências possíveis depois que os três primeiros caracteres são recebidos. 

No cabeçalho da solicitação, defina **chave de api** para uma chave de consulta ou de administrador, e **Content-Type** como application/json. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

As verificações de solicitação que todos os campos incluídos na sugestor (hotelName e categoria), retornando a seguinte resposta:

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

Para entregar o resultado esperado, o código do cliente seria renderizar os resultados como lista suspensa em uma barra de pesquisa.

## <a name="net-sdk-c"></a>SDK DO .NET (C#)

### <a name="1---index-with-a-suggester-construct"></a>1 - o índice com uma construção de sugestor

No SDK do .NET, use uma [classe Sugestor](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Encarregado da sugestão é uma coleção, mas pode levar apenas um item.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2 - consultar com o método de sugerir

O [DocumentsOperationsExtensions.Suggest método](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) é usado para retornar cadeias de caracteres de consulta sugeridos.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>Consulte também

+ [Explore a API REST usando o Postman](search-fiddler.md)
+ [Exemplo: Preenchimento automático](search-autocomplete-tutorial.md)
+ [Adicionar sugestores a um índice](index-add-suggesters.md)
+ [Adicionar uma classe de Sugestores usando o .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [Sugestões de chamada usando GET ou POST (API REST)](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [Chame sugestões usando SuggestWithHttpMessagesAsync (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) ou 