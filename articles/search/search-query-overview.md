---
title: Conceitos básicos de consulta no Azure Search | Microsoft Docs
description: Noções básicas para criar uma consulta de pesquisa no Azure Search, usando parâmetros para filtrar, selecionar e classificar resultados.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366439"
---
# <a name="query-fundamentals-in-azure-search"></a>Conceitos básicos de consulta no Azure Search

Uma definição de consulta no Azure Search é uma especificação completa de uma solicitação que inclui essas partes: ponto de extremidade de URL de serviço, índice de destino, chave de API usada para autenticar a solicitação, versão da API e uma cadeia de caracteres de consulta. 

A composição da cadeia de caracteres de consulta consiste em parâmetros, conforme definido em [Pesquisar API de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents). O mais importante é o parâmetro **search=**, que pode ser indefinido (`search=*`) mas, mais provavelmente consiste em termos, frases e operadores semelhantes ao exemplo a seguir:

```
"search": "seattle townhouse +\"lake\""
```

Outros parâmetros são usados para direcionar o processamento de consultas ou aprimorar a resposta. Exemplos de como os parâmetros são usados incluem pesquisa de escopo para campos específicos, definição de um modo de pesquisa para modular o desvio de precisão para recall e adição de uma contagem para que seja possível acompanhar os resultados. 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

Embora a definição de consulta seja fundamental, o esquema de índice é igualmente importante na maneira como especifica as operações permitidas campo a campo. Durante o desenvolvimento do índice, os atributos nos campos determinam as operações permitidas. Por exemplo, para qualificar na pesquisa de texto completo e inclusão nos resultados da pesquisa, um campo deverá ser marcado como *pesquisável* e *recuperável*.

No momento da consulta, a execução é sempre em relação a um índice, autenticado usando uma chave de API fornecida na solicitação. Não é possível unir índices ou criar estruturas de dados temporárias ou personalizadas como um destino de consulta.  

Os resultados da consulta são transmitidos como documentos JSON na API REST, embora, se você usar APIs do .NET, a serialização será interna. É possível formatar resultados, definindo parâmetros na consulta, selecionando campos específicos para o resultado

Para resumir, a substância da solicitação de consulta especifica o escopo e as operações: quais campos incluir na pesquisa, quais campos incluir no conjunto de resultados, se deve classificar ou filtrar, e assim por diante. Não especificado, uma consulta é executada em todos os campos pesquisáveis como uma operação de pesquisa de texto completo, retornando um conjunto de resultados não marcado em ordem arbitrária.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>Tipos de consultas: pesquisar e filtrar

o Azure Search oferece várias opções para a criação de consultas extremamente poderosas. Os dois tipos de consulta principais que você usará são `search` e `filter`. 

+ As consultas `search` examinam um ou mais termos em todos os campos *pesquisáveis* no índice e funcionam da maneira esperada para um mecanismo de pesquisa como Google ou Bing. Os exemplos na introdução usam o parâmetro `search`.

+ As consultas `filter` avaliam uma expressão booliana em todos os campos *filtráveis* em um índice. Diferentemente de `search`, a `filter` corresponde ao conteúdo exato de um campo, incluindo a diferenciação de maiúsculas e minúsculas nos campos da cadeia de caracteres.

É possível usar a pesquisa e filtrar em conjunto ou separadamente. Um filtro autônomo, sem uma cadeia de caracteres de consulta, é útil quando a expressão de filtro é capaz de qualificar totalmente os documentos de interesse. Sem uma cadeia de caracteres de consulta, não há nenhuma análise linguística ou lexical, nenhuma pontuação e nenhuma classificação. Observe que a cadeia de caracteres de pesquisa está vazia.

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

Usado em conjunto, o filtro é aplicado primeiro ao índice inteiro e, em seguida, a pesquisa é executada nos resultados do filtro. Os filtros, portanto, podem ser uma técnica útil para melhorar o desempenho da consulta, uma vez que reduzem o conjunto de documentos que a consulta de pesquisa precisa processar.

A sintaxe das expressões de filtro é um subconjunto da [linguagem de filtro OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Para as consultas de pesquisa, você pode usar a [sintaxe simplificada](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) ou a [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search), que são analisadas abaixo.


## <a name="choose-a-syntax-simple-or-full"></a>Escolha uma sintaxe: simples ou completa

O Azure Search funciona no Apache Lucene e fornece-lhe uma opção entre dois analisadores de consulta para tratamento de consultas especializadas e típicas. Solicitações de pesquisa típicas são formuladas usando a [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) padrão. Essa sintaxe dá suporte a vários operadores de pesquisa comuns, incluindo os operadores de precedência, E, OU, NÃO, frase e sufixo.

A [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), habilitada quando você adiciona **queryType=full** à solicitação, expõe a linguagem de consulta amplamente adotada e expressiva desenvolvida como parte do [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Usando essa sintaxe de consulta permite consultas especializadas:

+ [Consultas com escopo de campo](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [pesquisa difusa](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [pesquisa por proximidade](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [aumento de termos](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [pesquisa com expressão regular](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [pesquisa com curinga](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

Operadores boolianos são basicamente os mesmos em ambas as sintaxes, com formatos adicionais completos em Lucene:

+ [Operadores boolianos na sintaxe simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [Operadores boolianos na sintaxe Lucene completa](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>Elementos obrigatórios e opcionais

Ao enviar solicitações de pesquisa ao Azure Search, há vários parâmetros que podem ser especificados junto com as palavras reais digitadas na caixa de pesquisa de seu aplicativo. Esses parâmetros de consulta permitem conseguir maior controle do comando [full-text search experience](search-lucene-query-architecture.md).

Os elementos obrigatórios em uma solicitação de consulta incluem os componentes a seguir:

+ Ponto de extremidade de serviço e coleção de documentos de índice, expressos aqui como uma URL`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`.
+ Versão da API (somente REST), expressa como **api-version=**
+ Chave de API de administração ou consulta, expressa como **api-key=**
+ Cadeia de caracteres de consulta expressa como **search=**, que poderá não ser especificada se você quiser realizar uma pesquisa vazia. Também é possível enviar apenas uma expressão de filtro **$filter=**.
+ **queryType=**, simples ou completa, que poderá ser omitida se você quiser usar a sintaxe simples padrão.

Todos os outros parâmetros de pesquisa são opcionais.

## <a name="apis-and-tools-for-testing"></a>APIs e ferramentas para teste

A tabela a seguir lista as APIs e as abordagens baseadas em ferramentas para enviar consultas.

| Metodologia | DESCRIÇÃO |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Cliente que pode ser usado para consultar um índice do Azure Search.  <br/>[Saiba mais.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Pesquisar documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Métodos GET ou POST em um índice, usando parâmetros de consulta para entrada adicional.  |
| [Fiddler, Postman ou outra ferramenta de teste HTTP](search-fiddler.md) | Explica como configurar um cabeçalho e um corpo de solicitação para enviar consultas ao Azure Search.  |
| [Gerenciador de pesquisa no portal do Azure](search-explorer.md) | Fornece uma barra de pesquisa e opções para seleções de versão da API e índice. Os resultados são retornados como documentos JSON. <br/>[Saiba mais.](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>Gerenciar resultados da pesquisa

Os parâmetros na consulta podem ser usados para estruturar o conjunto de resultados das seguintes maneiras:

+ Limitar ou enviar em lote o número de documentos nos resultados (50 por padrão)
+ Selecionar campos para incluir nos resultados
+ Definir uma ordem de classificação
+ Adicionar realces de ocorrência para destacar termos correspondentes no corpo dos resultados da pesquisa

### <a name="tips-for-unexpected-results"></a>Dicas para resultados inesperados

Ocasionalmente, a substância e não a estrutura dos resultados são inesperadas. Quando os resultados da consulta não forem os esperados, você poderá tentar essas modificações de consulta para verificar se os resultados melhoram:

+ Altere `searchMode=any` (padrão) para `searchMode=all` para exigir correspondências em todos os critérios, em vez de em qualquer um dos critérios. Isso é especialmente verdadeiro quando os operadores boolianos estão incluídos na consulta.

+ Altere a técnica de consulta se o texto ou a análise lexical for necessária, mas o tipo de consulta impede o processamento linguístico. Na pesquisa de texto completo, o texto ou a análise lexical corrige automaticamente erros ortográficos, formas de palavras no plural e singular e até mesmo verbos ou substantivos irregulares. Para algumas consultas, como pesquisa difusa ou com curinga, a análise de texto não faz parte do pipeline de análise de consulta. Para alguns cenários, expressões regulares foram usadas como um solução alternativa. 

### <a name="paging-results"></a>Resultados da paginação
O Azure Search facilita implementar a paginação dos resultados da pesquisa. Usando os parâmetros `top` e `skip`, você pode emitir sem problemas as solicitações de pesquisa que permitem receber o conjunto total de resultados da pesquisa em subconjuntos gerenciáveis e ordenados, que permitem facilmente as boas práticas da IU de pesquisa. Ao receber esses subconjuntos menores de resultados, você também pode receber a contagem de documentos no conjunto total de resultados da pesquisa.

Você pode aprender mais sobre a paginação dos resultados da pesquisa no artigo [Como paginar os resultados da pesquisa no Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordenando resultados
Ao receber os resultados de uma consulta de pesquisa, você pode solicitar que o Azure Search apresente os resultados ordenados por valores em um campo específico. Por padrão, o Azure Search ordena os resultados da pesquisa com base na classificação de pontuação da pesquisa de cada documento, que é derivada de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se você quiser que o Azure Search retorne os resultados ordenados por um valor diferente da pontuação da pesquisa, poderá usar o `orderby` parâmetro de pesquisa. Você pode especificar o valor do parâmetro `orderby` para incluir os nomes de campo e as chamadas para a função [`geo.distance()` ](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) para obter os valores geoespaciais. Cada expressão pode ser seguida por `asc` para indicar que os resultados são solicitados na ordem ascendente e `desc` para indicar que os resultados são solicitados na ordem descendente. Ordem ascendente da classificação padrão.


### <a name="hit-highlighting"></a>Realce de ocorrência
No Azure Search, enfatizar a parte exata dos resultados da pesquisa que correspondem à consulta de pesquisa é mais fácil usando os parâmetros `highlight`, `highlightPreTag` e `highlightPostTag`. Você pode especificar quais campos *pesquisáveis* devem ter o texto correspondido enfatizado, bem como especificar as marcas da cadeia de caracteres exatas para anexar ao início e ao término do texto correspondente que a Azure Search retorna.

## <a name="see-also"></a>Consulte também

+ [Como a pesquisa de texto completo funciona no Azure Search (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](search-query-dotnet.md)
+ [Como consultar em REST](search-query-rest-api.md)
