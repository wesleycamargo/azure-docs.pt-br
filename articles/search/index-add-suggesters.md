---
title: Adicionar sugestores a um índice do Azure Search
description: Habilita os campos para ações de consulta de digitação antecipada, em que as consultas sugeridas são compostas de texto de campos em um índice do Azure Search.
ms.date: 02/13/2019
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
ms.openlocfilehash: fd4b29134fd45ed2888fbc81ded413ecf7286959
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308645"
---
# <a name="add-suggesters-to-an-azure-search-index"></a>Adicionar sugestores a um índice do Azure Search

Um **Sugestor** é um constructo do Azure Search que dá suporte ao recurso de [Sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) de "pesquisa conforme a digitação" e o recurso de [preenchimento automático (versão prévia)](search-autocomplete-tutorial.md). Antes de chamar a API de Sugestões, você precisa definir um **sugestor** em um índice para habilitar sugestões em campos específicos.

Embora um **sugestor** tenha várias propriedades, ele é principalmente uma coleção de campos para os quais você está habilitando a [API de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions). Por exemplo, um aplicativo de viagem talvez queira habilitar a pesquisa de digitação antecipada para destinos, cidades e atrações. Dessa forma, todos os três campos iriam para a coleção de campos.

Você pode ter apenas um recurso **sugestor** para cada índice (especificamente, um **sugestor** na coleção de **sugestores**).

## <a name="creating-a-suggester"></a>Criar um sugestor

Você pode criar um **sugestor** a qualquer momento, mas o impacto em seu índice varia de acordo com os campos.

+ Novos campos adicionados a um sugestor como parte da mesma atualização são os que têm menos impacto, já que nenhuma recompilação de índice é necessária.
+ Os campos existentes adicionados ao sugestor, no entanto, alteram a definição do campo e exigem uma recompilação completa do índice.

**Sugestores** funcionam melhor quando usados para sugerir documentos específicos, em vez de termos ou frases flexíveis. Os campos de melhores candidatos são títulos, nomes e outras frases relativamente curtas que podem identificar um item. Os campos repetitivos, como categorias e marcas, ou campos muito longos, como campos de comentários ou descrições, são menos eficazes.

Depois que um sugestor é criado, adicione a [API de sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions) em sua lógica de consulta para invocar o recurso.

Propriedades que definem um **sugestor** incluem o seguinte:

|Propriedade|DESCRIÇÃO|
|--------------|-----------------|
|`name`|O nome do **sugestor**. Use o nome do **sugestor** ao chamar as [Sugestões &#40;API REST do Serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions).|
|`searchMode`|A estratégia usada para pesquisar frases candidatas. O único modo com suporte atualmente é `analyzingInfixMatching`, que executa a correspondência flexível de frases no início ou no meio da frase.|
|`sourceFields`|Uma lista de um ou mais campos que são a fonte do conteúdo para obter sugestões. Somente os campos dos tipos `Edm.String` e `Collection(Edm.String)` podem ser fontes de sugestões. Somente os campos que não têm um analisador de idioma personalizado definido podem ser usados. |

## <a name="suggester-example"></a>Exemplo de sugestor
Um **sugestor** faz parte da definição do índice. Apenas um **sugestor** pode existir na coleção de **sugestores** na versão atual, junto com a coleção de **campos** e **scoringProfiles**.

```
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

## <a name="see-also"></a>Consulte também
[Criar índice &#40;API REST do Serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
[Atualizar o índice &#40;API de REST do serviço de Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/update-index)  
[Sugestões &#40;API de REST do serviço de Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/suggestions)  
[Operações de índice &#40;API de REST do serviço Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/index-operations)  
[REST do serviço de Azure Search](https://docs.microsoft.com/rest/api/searchservice/)  
[SDK do .NET paro Azure Search](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)
