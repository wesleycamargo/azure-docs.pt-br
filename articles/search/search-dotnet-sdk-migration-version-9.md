---
title: Atualizar para o SDK .NET do Azure Search versão 9 - Azure Search
description: Migre o código para o SDK do .NET do Azure Search versão 9 de versões mais antigas. Conheça as novidades e quais alterações de código são necessárias.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: f540bc304920073bcd823adcf6c9dd47cb2cf93b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159744"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-9"></a>Atualizando para o SDK do .NET do Azure Search versão 9

Se você estiver usando a versão 7.0-preview ou mais antiga do [o SDK do .NET do Azure Search](https://aka.ms/search-sdk), este artigo o ajudará a atualizar seu aplicativo para usar a versão 9.

> [!NOTE]
> Se você quiser usar a versão 8.0-preview para avaliar os recursos que ainda não estão disponíveis em geral, você também pode seguir as instruções neste artigo para atualizar a visualização 8.0 de versões anteriores.

Para obter uma explicação mais geral do SDK, incluindo exemplos, confira [Como usar o Azure Search de um aplicativo .NET](search-howto-dotnet-sdk.md).

Versão 9 do SDK do .NET do Azure Search contém muitas alterações de versões anteriores. Algumas dessas alterações são significativas, mas eles devem exigir apenas feitas pequenas alterações no seu código. Confira [Etapas da atualização](#UpgradeSteps) para obter instruções sobre como alterar o seu código para usar a nova versão do SDK.

> [!NOTE]
> Se você estiver usando a versão 4.0-preview ou mais antiga, você deve atualizar primeiro para a versão 5 e, em seguida, atualize para a versão 9. Ver [atualizar para o SDK .NET do Azure Search versão 5](search-dotnet-sdk-migration-version-5.md) para obter instruções.
>
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>O que há de novo na versão 9
Versão 9 do SDK do .NET do Azure Search tem como alvo a versão mais recente versão disponível da API de REST do Azure Search, especificamente 2019-05-06. Isso possibilita o uso de vários recursos novos do Azure Search por meio de um aplicativo .NET, incluindo o seguinte:

* [Pesquisa cognitiva](cognitive-search-concept-intro.md) é um recurso de inteligência Artificial no Azure Search, usado para extrair texto de imagens, blobs e outras fontes de dados não estruturados - enriquecendo o conteúdo para torná-lo mais pesquisáveis no índice de Azure Search.
* Suporte para [tipos complexos](search-howto-complex-data-types.md) permite que você modele praticamente qualquer estrutura aninhada de JSON em um índice de Azure Search.
* [Preenchimento automático](search-autocomplete-tutorial.md) fornece uma alternativa para o **sugerir** API para implementar o comportamento de pesquisa-como-você-type. Preenchimento automático "conclusão" a palavra ou frase que um usuário está digitando no momento.
* [Modo de análise de JsonLines](search-howto-index-json-blobs.md), parte do Blob do Azure de indexação, cria um documento de pesquisa por entidade JSON que é separado por uma nova linha.

### <a name="new-preview-features-in-version-80-preview"></a>Novos recursos de visualização na versão 8.0-preview
Versão 8.0-preview do SDK do .NET do Azure Search tem como alvo a versão 2017-11-11-versão prévia da API. Esta versão inclui todos os mesmos recursos da versão 9, além de:

* [As chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) para criptografia em repouso do lado do serviço é um novo recurso de visualização. Além de interno criptografia em repouso gerenciado pela Microsoft, você pode aplicar uma camada adicional de onde você está a única proprietária das chaves de criptografia.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Primeiro, atualize sua referência NuGet para `Microsoft.Azure.Search` usando o Console do Gerenciador de Pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando “Gerenciar pacotes NuGet...” no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. Dependendo de como o código está estruturado, ele poderá ser recriado com êxito. Nesse caso, você está pronto para começar!

Se a compilação falhar, você precisará corrigir cada erro de compilação. Ver [alterações significativas na versão 9](#ListOfChanges) para obter detalhes sobre como resolver cada potencial de erro de compilação.

Você pode ver avisos de build adicionais relacionados a propriedades ou métodos obsoletos. Os avisos incluirão instruções sobre o que deve ser usado no lugar do recurso preterido. Por exemplo, se seu aplicativo usa o `DataSourceType.DocumentDb` propriedade, você deve receber um aviso que diz "Este membro foi preterido. Use CosmosDb".

Após corrigir todos os avisos ou erros de build, você poderá fazer alterações no aplicativo para aproveitar as novas funcionalidades, se desejar. Novos recursos no SDK são detalhados em [o que há de novo na versão 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Alterações significativas na versão 9

Há várias alterações significativas na versão 9 que pode exigir alterações de código, além da recompilação do seu aplicativo.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultará em erros de compilação, mas são tecnicamente significativas, pois eles interrompem a compatibilidade binária com assemblies que dependem de versões anteriores dos assemblies do SDK do .NET do Azure Search. Essas alterações não são listadas abaixo. Recompile seu aplicativo ao atualizar para a versão 9 para evitar quaisquer problemas de compatibilidade binária.

### <a name="making-properties-immutable"></a>Tornando as propriedades imutáveis

As propriedades públicas de várias classes de modelo agora são imutáveis. Se você precisar criar personalizadas instâncias dessas classes para teste, você pode usar os novos construtores com parâmetros:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Alterações ao campo

O `Field` classe foi alterado, agora que ele também pode representar campos complexos.

O seguinte `bool` propriedades agora são que permitem valor nulas:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Isso ocorre porque essas propriedades agora devem ser `null` no caso de campos complexos. Se você tiver o código que lê essas propriedades, ela deverá estar preparado para lidar com `null`. Observe que todas as outras propriedades de `Field` sempre foram e continuam a ser anulável, e algumas delas também será `null` no caso de campos complexos – especificamente o seguinte:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

O construtor sem parâmetros de `Field` foi feita `internal`. De agora em diante, cada `Field` requer um tipo de dados e o nome explícito no momento da construção.

### <a name="simplification-of-batch-and-results-types"></a>Simplificação dos tipos de lotes e resultados

Na versão 7.0-preview e anterior, as várias classes que encapsulam os grupos de documentos eram estruturadas em hierarquias de classe parallel:

  -  `DocumentSearchResult` e `DocumentSearchResult<T>` herdado de `DocumentSearchResultBase`
  -  `DocumentSuggestResult` e `DocumentSuggestResult<T>` herdado de `DocumentSuggestResultBase`
  -  `IndexAction` e `IndexAction<T>` herdado de `IndexActionBase`
  -  `IndexBatch` e `IndexBatch<T>` herdado de `IndexBatchBase`
  -  `SearchResult` e `SearchResult<T>` herdado de `SearchResultBase`
  -  `SuggestResult` e `SuggestResult<T>` herdado de `SuggestResultBase`

Os tipos derivados sem um parâmetro de tipo genérico foram deve ser usado em cenários de "tipo dinâmico" e supõe-se o uso do `Document` tipo.

A partir da versão 8.0-preview, as classes base e classes derivadas de não-genérica todos foram removidas. Para cenários de tipo dinâmico, você pode usar `IndexBatch<Document>`, `DocumentSearchResult<Document>`e assim por diante.
 
### <a name="removal-of-extensibleenum"></a>Remoção de ExtensibleEnum

O `ExtensibleEnum` classe base tiver sido removido. Todas as classes derivam dele agora são structs, tais como `AnalyzerName`, `DataType`, e `DataSourceType` por exemplo. Seus `Create` métodos também foram removidos. Você pode remover chamadas para `Create` , pois esses tipos são implicitamente conversíveis de cadeias de caracteres. Se o que resulta em erros de compilador, você pode invocar explicitamente o operador de conversão por meio de conversão para resolver a ambiguidade de tipos. Por exemplo, você pode alterar o código como este:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

para isto:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Propriedades que são mantidos opcionais valores desses tipos agora são tipadas explicitamente como anuláveis para que elas continuem a ser opcional.

### <a name="removal-of-facetresults-and-hithighlights"></a>Remoção de FacetResults e HitHighlights

O `FacetResults` e `HitHighlights` foram removidas. Agora, os resultados da faceta são digitados como `IDictionary<string, IList<FacetResult>>` e pressione destaca como `IDictionary<string, IList<string>>`. Uma maneira rápida de resolver erros de compilação introduzidos por essa alteração é adicionar `using` aliases na parte superior de cada arquivo que usa os tipos removidos. Por exemplo: 

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Alterar para SynonymMap 

O construtor `SynonymMap` não tem mais um parâmetro `enum` para `SynonymMapFormat`. Essa enumeração tinha apenas um valor e, portanto, era redundante. Se você observar erros de build como um resultado disso, basta remover as referências para o parâmetro `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Alterações de classe de modelo diversos

O `AutocompleteMode` propriedade de `AutocompleteParameters` não é anulável. Se você tiver um código que atribui essa propriedade como `null`, simplesmente poderá removê-lo e a propriedade será inicializada automaticamente para o valor padrão.

A ordem dos parâmetros para o `IndexAction` construtor mudou agora que esse construtor é gerado automaticamente. Em vez de usar o construtor, é recomendável usar os métodos de fábrica `IndexAction.Upload`, `IndexAction.Merge`e assim por diante.

### <a name="removed-preview-features"></a>Recursos de visualização removidos

Se você estiver atualizando da versão 8.0-preview para a versão 9, esteja ciente de que a criptografia com chaves gerenciadas pelo cliente foi removida, pois esse recurso ainda está em visualização. Especificamente, o `EncryptionKey` propriedades de `Index` e `SynonymMap` foram removidos.

Se seu aplicativo tiver uma dependência rígida sobre esse recurso, você não poderá atualizar para a versão 9 do SDK do .NET do Azure Search. Você pode continuar a usar a versão 8.0-preview. No entanto, lembre-se que **não é recomendável usar SDKs de visualização em aplicativos de produção**. Os recursos de visualização destinam-se apenas a fins de avaliação e podem ser alterados.

> [!NOTE]
> Se você tiver criado criptografado índices ou sinônimo mapas usando a versão 8.0-preview do SDK, você ainda poderá usá-las e modificar suas definições usando a versão 9 do SDK sem afetar negativamente o seu status de criptografia. Versão 9 do SDK não enviará o `encryptionKey` propriedade para a API REST e, como resultado da API REST não alterará o status da criptografia do recurso. 

### <a name="behavioral-change-in-data-retrieval"></a>Alteração de comportamento na recuperação de dados

Se você estiver usando o "dinamicamente de tipo" `Search`, `Suggest`, ou `Get` APIs que retornam instâncias do tipo `Document`, lembre-se de que eles agora desserializar matrizes vazias do JSON para `object[]` em vez de `string[]`.

## <a name="conclusion"></a>Conclusão
Se precisar de mais detalhes sobre como usar o SDK do .NET do Azure Search, confira o [Tutorial .NET](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se você encontrar problemas, fique à vontade para solicitar ajuda no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, você poderá apresentar um problema no [repositório GitHub sobre o SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Não deixe de colocar o prefixo "[Azure Search]" no título do problema.

Obrigado por usar o Azure Search!
