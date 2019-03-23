---
title: Adicionar consultas de digitação antecipada para um índice - Azure Search
description: Habilite ações de consulta de preenchimento automático no Azure Search criando sugestores e formular solicitações que invocam o preenchimento automático ou autosuggested termos de consulta.
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370645"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Adicionar sugestores a um índice para digitação antecipada no Azure Search

Um **sugestor** é uma construção em um [índice de Azure Search](search-what-is-an-index.md) que dá suporte a uma experiência "Pesquisar-como-você-type". Ele contém uma lista de campos para o qual você deseja habilitar entradas de consulta de digitação antecipada. Há duas variantes de digitação antecipada: [ *AutoCompletar* ](search-autocomplete-tutorial.md) conclui o termo ou frase que você está digitando, [ *sugestão automática* ](search-autosuggest-example.md) fornece um breve lista de termos ou frases que você pode selecionar como uma consulta de entrada. Você viu, sem dúvida, esses comportamentos antes de nos mecanismos de pesquisa comerciais.

![Comparação visual do preenchimento automático e de sugestão automática](./media/index-add-suggesters/visual-comparison-suggest-complete.png "comparação Visual do preenchimento automático e de sugestão automática")

Para implementar esses comportamentos no Azure Search, há um componente de indexação e consulta. 

+ Em um índice, adicione um sugestor. Você pode usar o portal, API REST ou SDK do .NET para criar um sugestor. 

+ Em uma consulta, especifique a ação de uma sugestão automática ou preenchimento automático. 

> [!Important]
> Preenchimento automático está atualmente em versão prévia, disponível na visualização de APIs REST e SDK do .NET e não tem suporte para aplicativos de produção. 

Suporte de digitação antecipada está habilitado em uma base por campo. Se você quiser uma experiência semelhante àquela indicada na captura de tela, você pode implementar os dois comportamentos de digitação antecipada dentro da mesma solução de pesquisa. Destino de ambas as solicitações de *documentos* coleção de índice específico e as respostas são retornadas depois que um usuário tenha fornecido pelo menos uma cadeia de caracteres de entrada de três caracteres.

## <a name="create-a-suggester"></a>Criar um sugestor

Embora um sugestor tem várias propriedades, é principalmente uma coleção de campos para o qual você estiver habilitando uma experiência de digitação antecipada. Por exemplo, um aplicativo de viagem talvez queira habilitar a pesquisa de digitação antecipada para destinos, cidades e atrações. Dessa forma, todos os três campos iria na coleção de campos.

Para criar um sugestor, adicione um para um esquema de índice. Você pode ter um sugestor em um índice (especificamente, um sugestor na coleção de sugestores). 

Na API REST, você pode adicionar sugestões por meio [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) ou [Atualizar índice](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

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

Principais pontos a observar sobre sugestores é que há um nome (sugestores são referenciados pelo nome em uma solicitação), um modo de pesquisa (atualmente, apenas um, "analyzingInfixMatching") e a lista de campos para os quais a digitação antecipada está habilitada. 

Depois que um sugestor é criado, adicione a [API de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) em sua lógica de consulta para invocar o recurso.

### <a name="property-reference"></a>Referência de propriedade

Propriedades que definem um sugestor incluem o seguinte:

|Propriedade      |DESCRIÇÃO      |
|--------------|-----------------|
|`name`        |O nome do sugestor. Você usar o nome do sugestor ao chamar o [API REST de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou [API REST de preenchimento automático (visualização)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |A estratégia usada para pesquisar frases candidatas. O único modo com suporte atualmente é `analyzingInfixMatching`, que executa a correspondência flexível de frases no início ou no meio da frase.|
|`sourceFields`|Uma lista de um ou mais campos que são a fonte do conteúdo para obter sugestões. Somente os campos dos tipos `Edm.String` e `Collection(Edm.String)` podem ser fontes de sugestões. Somente os campos que não têm um analisador de idioma personalizado definido podem ser usados.<p/>Especifique apenas os campos que se prestam para uma resposta esperada e apropriada, se ele é uma cadeia de caracteres completa em uma barra de pesquisa ou uma lista suspensa.<p/>Um nome de hotel é um bom candidato porque ele tem a precisão. Campos detalhados como comentários e descrições são muito densos. Da mesma forma, os campos repetitivos, como categorias e marcas, são menos eficazes. Nos exemplos, nós a incluímos "category" mesmo assim para demonstrar que você pode incluir vários campos. |

### <a name="index-rebuilds-for-existing-fields"></a>As recompilações de índice para campos existentes

Sugestores contêm campos, e se você estiver adicionando um sugestor a um índice existente ou alterar sua composição de campo, você provavelmente terá que recriar o índice.

| Ação | Impacto |
|--------|--------|
| Criar novos campos e criar um novo sugestor simultaneamente na mesma atualização | Menos impacto. Se um índice contém campos adicionados anteriormente, a adição de novos campos e uma nova sugestão não tem impacto sobre os campos existentes. |
| Adicionar campos já existentes a um sugestor | Alto impacto. Adicionando um campo altera a definição de campo, a necessidade de uma [recriação completa](search-howto-reindex.md).|

## <a name="use-a-suggester"></a>Use um sugestor

Como mencionado anteriormente, você pode usar um sugestor sugestão automática, preenchimento automático ou ambos. 

Um sugestor é referenciado na solicitação, juntamente com a operação. Por exemplo, em uma chamada GET REST, especifique `suggest` ou `autocomplete` na coleção de documentos. Para REST, depois que um sugestor é criado, use o [API de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) ou o [API de preenchimento automático (visualização)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) em sua lógica de consulta.

Para .NET, use [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) ou [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Exemplos demonstram cada solicitação:

+ [Adicionar para seleções da consulta de lista suspensa de sugestão automática](search-autosuggest-example.md)

+ [Adicionar preenchimento automático para entradas de termo parcial no Azure Search](search-autocomplete-tutorial.md) (em versão prévia) 

## <a name="sample-code"></a>Exemplo de código

O [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) exemplo contém ambas C# e o código Java e demonstra uma construção encarregado da sugestão, sugestão automática, preenchimento automático e navegação facetada. 

Ele usa uma área restrita do serviço de pesquisa do Azure e um índice pré-carregado para que tudo o que você precisa fazer é pressionar F5 para executá-lo. Nenhuma assinatura ou a entrada necessária.

## <a name="next-steps"></a>Próximas etapas

Examine os exemplos a seguir para ver como as solicitações são formuladas:

+ [Exemplo de consulta autosuggested](search-autosuggest-example.md) 
+ [Exemplo de consulta preenchida automaticamente (visualização)](search-autocomplete-tutorial.md) 
