---
title: Criar uma definição de índice e conceitos - Azure Search
description: Introdução para indexar termos e conceitos no Azure Search, incluindo partes de componentes e estrutura física.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/01/2019
ms.custom: seodec2018
ms.openlocfilehash: 77f4b597ad4b87db7e720dd57191c6b192a4c93b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000942"
---
# <a name="create-a-basic-index-in-azure-search"></a>Criar um índice básico no Azure Search

No Azure Search, um *índice* é o repositório persistente de *documentos* e outras construções usados para pesquisa filtrada e de texto completo em um serviço do Azure Search. Conceitualmente, um documento é uma única unidade de dados pesquisáveis no índice. Por exemplo, uma loja de comércio eletrônico pode ter um documento para cada item vendido, uma agência de notícias pode ter um documento para cada artigo e assim por diante. Mapeando esses conceitos para equivalentes de banco de dados mais conhecidos: um *índice* é conceitualmente semelhante a uma *tabela* e *documentos* são equivalentes a *linhas* em uma tabela.

Ao adicionar ou carregar um índice, o Azure Search criará estruturas físicas com base no esquema que você fornecer. Por exemplo, se um campo no índice estiver marcado como pesquisável, um índice invertido será criado para esse campo. Posteriormente, ao adicionar ou carregar documentos ou enviar consultas de pesquisa para o Azure Search, você estará enviando solicitações para um índice específico no serviço de pesquisa. Carregar campos com valores de documento é chamado de *indexação* ou ingestão de dados.

É possível criar um índice no portal, na [API REST](search-create-index-rest-api.md) ou no [SDK do .NET](search-create-index-dotnet.md).

## <a name="components-of-an-index"></a>Componentes de um índice

Esquematicamente, um índice do Azure Search é composto dos seguintes elementos. 

A [*coleção de campos*](#fields-collection) normalmente é a maior parte de um índice, onde cada campo é nomeado, digitado e atribuído com comportamentos permitidos que determinam como é usado. Outros elementos incluem [sugestores](#suggesters), [perfis de pontuação](#scoring-profiles), [analisadores](#analyzers) com partes de componentes para dar suporte à personalização e opções de [CORS](#cors).

```json
{  
  "name": (optional on PUT; required on POST) "name_of_index",  
  "fields": [  
    {  
      "name": "name_of_field",  
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",  
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),  
      "filterable": true (default) | false,  
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),  
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),  
      "key": true | false (default, only Edm.String fields can be keys),  
      "retrievable": true (default) | false,  
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }  
  ],  
  "suggesters": [  
    {  
      "name": "name of suggester",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": ["field1", "field2", ...]  
    }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "name of scoring profile",  
      "text": (optional, only applies to searchable fields) {  
        "weights": {  
          "searchable_field_name": relative_weight_value (positive #'s),  
          ...  
        }  
      },  
      "functions": (optional) [  
        {  
          "type": "magnitude | freshness | distance | tag",  
          "boost": # (positive number used as multiplier for raw score != 1),  
          "fieldName": "...",  
          "interpolation": "constant | linear (default) | quadratic | logarithmic",  
          "magnitude": {  
            "boostingRangeStart": #,  
            "boostingRangeEnd": #,  
            "constantBoostBeyondRange": true | false (default)  
          },  
          "freshness": {  
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)  
          },  
          "distance": {  
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)  
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)  
          },  
          "tag": {  
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)  
          }  
        }  
      ],  
      "functionAggregation": (optional, applies only when functions are specified)   
        "sum (default) | average | minimum | maximum | firstMatching"  
    }  
  ],  
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",  
  "corsOptions": (optional) {  
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],  
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)  
  }  
}
```

## <a name="fields-collection-and-attribution"></a>Coleta e atribuição de campos
Quando você define o esquema, deve especificar o nome, tipo e atributos de cada campo no índice. O tipo de campo classifica os dados armazenados nesse campo. Os atributos são definidos em campos individuais para especificar como o campo será usado. A tabela a seguir enumera os tipos e atributos que você pode especificar.

### <a name="data-types"></a>Tipos de dados
| Type | DESCRIÇÃO |
| --- | --- |
| *Edm.String* |O texto que opcionalmente pode ser indexado para a pesquisa de texto completo (separação de palavras, derivação etc). |
| *Collection(Edm.String)* |Uma lista de cadeias de caracteres que opcionalmente podem ser indexadas para a pesquisa de texto completo. Não há nenhum limite teórico superior no número de itens em uma coleção, mas o limite superior de 16 MB no tamanho da carga se aplica às coleções. |
| *Edm.Boolean* |Contém valores true/false. |
| *Edm.Int32* |Valores inteiros de 32 bits. |
| *Edm.Int64* |Valores inteiros de 64 bits. |
| *Edm.Double* |Dados numéricos de dupla precisão. |
| *Edm.DateTimeOffset* |Valores de data e hora representados no formato OData V4 (por exemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` ou `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Um ponto que representa uma localização geográfica em todo o mundo. |

Você pode encontrar informações mais detalhadas sobre os [tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) do Azure Search.

### <a name="index-attributes"></a>Atributos de índice
| Atributo | DESCRIÇÃO |
| --- | --- |
| *Chave* |Uma cadeia de caracteres que fornece a ID exclusiva de cada documento, usada para pesquisar documentos. Todos os índices devem ter uma chave. Somente um campo pode ser a chave e seu tipo deve ser definido para Edm.String. |
| *Recuperável* |Especifica se um campo pode ser retornado em um resultado da pesquisa. |
| *Filtrável* |Permite que o campo seja usado nas consultas de filtro. |
| *Classificável* |Permite que uma consulta classifique os resultados da pesquisa usando esse campo. |
| *Com faceta* |Permite que um campo seja usado em uma estrutura de [navegação mista](search-faceted-navigation.md) para a filtragem autodirecionada do usuário. Normalmente, os campos que contêm valores repetidos que você pode usar para agrupar vários documentos (por exemplo, vários documentos que estejam em uma única categoria de marca ou de serviço) funcionam melhor como facetas. |
| *Pesquisável* |Marca o campo como texto completo pesquisável. |

Você pode encontrar informações mais detalhadas sobre os [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/Create-Index) do Azure Search.

## <a name="suggesters"></a>Sugestores
Um sugestor é uma seção do esquema que define quais campos em um índice são usados para dar suporte a consultas de preenchimento automático ou digitação antecipada em pesquisas. Normalmente, as cadeia de caracteres de pesquisa parciais são enviadas para as Sugestões (API REST do Serviço Azure Search), enquanto o usuário digita uma consulta de pesquisa e a API retorna um conjunto de frases sugeridas. Um sugestor que você define no índice determina quais campos são usados para construir os termos de pesquisa de preenchimento automático. Para obter mais informações, consulte [Adicionar sugestores](index-add-suggesters.md) para detalhes de configuração.

## <a name="scoring-profiles"></a>Perfis de pontuação

Um perfil de pontuação é uma seção do esquema que define comportamentos de pontuação personalizados que permitem influenciar quais itens são exibidos em uma posição mais alta nos resultados da pesquisa. Perfis de pontuação são compostos de funções e pesos de campos. Para usá-las, você pode especificar um perfil por nome na sequência de consulta.

Um padrão de pontuação perfil funciona nos bastidores para calcular uma pontuação para cada item em um conjunto de resultados de pesquisa. Você pode usar o perfil de pontuação interno e sem o nome. Como alternativa, defina defaultScoringProfile para usar um perfil personalizado como o padrão, invocado sempre que um perfil personalizado não for especificado na cadeia de caracteres de consulta.

Para obter mais informações, consulte [Adicionar perfis de pontuação](index-add-scoring-profiles.md).

## <a name="analyzers"></a>Analisadores

O elemento analisadores define o nome do analisador de linguagem a ser usado para o campo. Para o conjunto de valores permitidos, consulte [Analisadores de linguagem no Azure Search](index-add-language-analyzers.md). Essa opção pode ser usada somente com campos pesquisáveis e não pode ser definida com **searchAnalyzer** ou **indexAnalyzer**. Depois que o analisador for escolhido, ele não poderá ser alterado para o campo.

## <a name="cors"></a>CORS

O JavaScript do lado do cliente não pode chamar nenhuma API por padrão, pois o navegador impedirá todas as solicitações entre origens. Para permitir ao índice as consultas entre origens, habilite o CORS (Compartilhamento de Recursos entre Origens) definindo o atributo **corsOptions**. Por motivos de segurança, apenas APIs de consulta dão suporte para CORS. 

As seguintes opções podem ser definidas para CORS:

+ **allowedOrigins** (obrigatório): Essa é uma lista de origens que receberão acesso ao índice. Isso significa que qualquer código JavaScript atendendo a partir dessas origens terá permissão para consultar o índice (supondo que ele forneça a chave de API correta). Cada origem é tipicamente da forma, `protocol://<fully-qualified-domain-name>:<port>` embora `<port>` seja frequentemente omitida. Consulte [Compartilhamento de recursos entre origens (Wikipédia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para obter mais detalhes.

  Se você quiser permitir acesso a todas as origens, inclua `*` como um único item na matriz **allowedOrigins**. *Essa não é uma prática recomendada para serviços de pesquisa de produção*, mas geralmente é útil para desenvolvimento e depuração.

+ **maxAgeInSeconds** (opcional): Os navegadores usam esse valor para determinar a duração (em segundos) para armazenar em cache as respostas CORS de simulação. Esse deve ser um inteiro não negativo. Quanto maior for esse valor, melhor será o desempenho, porém, mais tempo levará para que as alterações de política CORS entrem em vigor. Se ele não for definido, uma duração padrão de cinco minutos será usada.

## <a name="next-steps"></a>Próximas etapas

Com o conhecimento adquirido sobre a composição de índice, você poderá continuar no portal para criar seu primeiro índice.

> [!div class="nextstepaction"]
> [Adicionar um índice (portal)](search-create-index-portal.md)