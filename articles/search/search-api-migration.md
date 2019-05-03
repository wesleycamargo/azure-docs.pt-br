---
title: Atualizar para a última versão da API REST do serviço Azure Search – Azure Search
description: Examine as diferenças nas versões de API e saiba quais ações são necessárias para migrar o código existente para a versão mais recente da API REST do serviço Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 286d8bbc01b5916e842c196aed5a49ef1c76bc3c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025192"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Atualizar para a última versão da API REST do serviço Azure Search
Se você estiver usando uma versão anterior da [API de REST do serviço Azure Search](https://docs.microsoft.com/rest/api/searchservice/), este artigo o ajudará a atualizar seu aplicativo para usar a versão de API disponível mais recente, 2019-05-06.

Versão de 2019-05-06 da API REST contém algumas alterações de versões anteriores. Elas são principalmente compatíveis com versões anteriores. Portanto, a alteração do código deve exigir apenas um mínimo de esforço, dependendo da versão que você estava usando antes. [Etapas para atualizar](#UpgradeSteps) descreve as alterações de código necessárias para utilizar novos recursos.

> [!NOTE]
> Uma instância do serviço Azure Search dá suporte a versões um intervalo da API REST, incluindo os mais antigos. Você pode continuar a usar essas versões de API, mas é recomendável migrar seu código para a versão mais recente para que você possa acessar novos recursos.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>O que há de novo na versão de 2019-05-06
Versão de 2019-05-06 é a versão mais recente disponível da API de REST do serviço de pesquisa do Azure. Recursos que têm a transição para o status disponível nesta versão de API incluem:

* [Preenchimento automático](index-add-suggesters.md) é um recurso de digitação antecipada que conclui uma entrada de termo parcialmente especificado.

* [Tipos complexos](search-howto-complex-data-types.md) fornece suporte nativo para dados de objeto estruturado em um índice de Azure Search.

* [Modos de análise de JsonLines](search-howto-index-json-blobs.md), parte do Blob do Azure de indexação, cria um documento de pesquisa por entidade JSON que é separado por uma nova linha.

* [Pesquisa cognitiva](cognitive-search-concept-intro.md) fornece a indexação que aproveita os mecanismos de enriquecimento da inteligência Artificial dos serviços Cognitivos.

Várias versões de recurso de visualização coincidirem com essa atualização estará disponível. Para revisar a lista de novos recursos de visualização, consulte [-versão da api REST de pesquisa de 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Alterações de última hora

Que contém a seguinte funcionalidade de código existente interromperá na api-version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexador do Azure Cosmos DB - datasource é agora "tipo": "cosmosdb"

Se você estiver usando um [indexador do Cosmos DB](search-howto-index-cosmosdb.md ), você deve alterar `"type": "documentdb"` para `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Erros de resultado de execução do indexador não têm o status

A estrutura de erro de execução do indexador anteriormente tinha uma `status` elemento. Esse elemento foi removido porque ele não estava fornecendo informações úteis.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Fonte de dados do indexador API não retorna cadeias de caracteres de conexão

Da API de versões de 2019-05-06 e 2019-05-06-Preview e versões posteriores, a fonte de dados API não retorna mais cadeias de caracteres de conexão na resposta de qualquer operação REST. Nas versões anteriores da API, para fontes de dados criadas usando POST, o Azure Search retornado **201** seguido pela resposta de OData, que continha a cadeia de caracteres de conexão em texto sem formatação.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Chamada de reconhecimento de entidade habilidades cognitivas foi descontinuada

Se você chamar [reconhecimento de entidade de nome](cognitive-search-skill-named-entity-recognition.md) habilidade em seu código, a chamada falhará. A funcionalidade de substituição está [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md). Você deve ser capaz de substituir a referência de habilidade com nenhuma outra alteração. A assinatura de API é o mesmo para ambas as versões. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização
Se você estiver atualizando de uma versão anterior do GA, 2017-11-11 ou 2016-09-01, você provavelmente não precisará fazer quaisquer alterações no código, diferente de alterar o número de versão. As únicas situações em que talvez seja necessário alterar o código ocorrem quando:

* O código falha quando propriedades não reconhecidas são retornadas em uma resposta da API. Por padrão, o aplicativo deve ignorar propriedades que não entende.

* O código persiste solicitações de API e tenta reenviá-las à nova versão da API. Por exemplo, isso poderá acontecer se o aplicativo persistir tokens de continuação retornados da API de Pesquisa (para obter mais informações, procure `@search.nextPageParameters` na [Referência de API de Pesquisa](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Se alguma dessas situações se aplicar a você, talvez seja necessário alterar o código da maneira adequada. Caso contrário, nenhuma alteração será necessária a menos que você deseja começar a usar o [novos recursos](#WhatsNew) da versão de 2019-05-06.

Se você estiver atualizando de uma versão de API de visualização, os itens acima também se aplica, mas você também deve estar ciente de que alguns recursos de visualização não estão disponíveis na versão de 2019-05-06:

* [Consultas "Mais como isso"](search-more-like-this.md) continuam a ser um recurso apenas para visualização.

Se seu código usa esses recursos, você não poderá atualizar para a API versão 2019-05-06 sem remover o uso deles.

> [!IMPORTANT]
> As APIs em visualização servem para teste e avaliação e não devem ser usadas em ambientes de produção.
> 

### <a name="upgrading-complex-types"></a>Atualizando tipos complexos

Se seu código usa os tipos complexos com as versões mais antigas do preview API 2017-11-11-Preview ou 2016-09-01-Preview, há alguns limites de novas e alteradas na versão de 2019-05-06 dos quais você precisa estar ciente:

+ Os limites na profundidade dos subcampos e o número de coleções complexas por índice tem sido reduzidos. Se você criou os índices que exceder esses limites usando as versões de api de visualização, qualquer tentativa de atualizar ou recriá-los usando a API versão 2019-05-06 falhará. Se isso se aplica a você, será preciso reprojete o esquema para se ajustar dentro dos limites de novo e, em seguida, recriar o índice.

+ Há um limite de novo na versão de api de 2019-05-06 no número de elementos de coleções complexas por documento. Se você criou os índices com documentos que exceder esses limites usando as versões de api de visualização, qualquer tentativa de reindexar os dados usando a versão da api de 2019-05-06 falhará. Se isso se aplica a você, você precisará reduzir o número de elementos de coleção complexa por documento antes de reindexação de seus dados.

Para obter mais informações, consulte [limites de serviço para o Azure Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Como atualizar uma estrutura de tipo complexo antigo

Se seu código está usando tipos complexos com uma das versões mais antigas de API de visualização, você pode estar usando um formato de definição de índice que tem esta aparência:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Um novo formato de árvore para definir os campos de índice foi introduzido na versão 2017-11-11-versão prévia da API. No novo formato, cada campo complexo tem uma coleção de campos onde seus sub-campos são definidos. Na API versão 2019-05-06, esse novo formato é usado exclusivamente e tentar criar ou atualizar um índice usando o formato antigo falhará. Se você tiver índices criados usando o formato antigo, você precisará usar a versão 2017-11-11-versão prévia da API para atualizá-los para o novo formato antes que eles possam ser gerenciados usando a API versão 2019-05-06.

Você pode atualizar índices "simples" para o novo formato com as etapas a seguir usando a versão 2017-11-11-versão prévia da API:

1. Execute uma solicitação GET para recuperar seu índice. Se já estiver no novo formato, você terá concluído.

2. Converter o índice do formato "simples" para o novo formato. Você precisará escrever código para isso, já que não há nenhum código de exemplo disponível no momento da redação deste artigo.

3. Execute uma solicitação PUT para atualizar o índice para o novo formato. Certifique-se de não alterar quaisquer outros detalhes do índice, como a capacidade de pesquisa/filterability de campos, uma vez que isso não é permitido pela API de índice de atualização.

> [!NOTE]
> Não é possível gerenciar os índices criados com o formato antigo "simples" do portal do Azure. Atualize seus índices da representação "simples" para a representação de "árvore" assim que for possível.

## <a name="next-steps"></a>Próximas etapas

Examine a documentação de referência de API de REST do serviço Azure Search. Se você encontrar problemas, solicitar ajuda no [StackOverflow](https://stackoverflow.com/) ou [contate o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)

