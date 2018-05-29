---
title: Criar Indexador (Azure Search Service REST api-version=2017-11-11-Versão prévia)
description: Na API de visualização, os indexadores são estendidos para incluir parâmetros de outputFieldMapping usados para mapear a saída de enriquecimento para um campo em um índice do Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.author: luisca
ms.date: 05/01/2018
ms.prod: azure
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: language-reference
ms.openlocfilehash: d47b14342caf0312e052584d20f1a9c86ca29cad
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786915"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Criar Indexador (Azure Search Service REST api-version=2017-11-11-Versão prévia)

Essa referência de API é uma versão de visualização específicos da documentação, que abrangem os aprimoramentos da pesquisa cognitivas para indexação.

Como com a versão [geralmente disponível](https://docs.microsoft.com/rest/api/searchservice/create-indexer), você pode criar um novo indexador dentro de um serviço do Azure Search usando uma solicitação HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=[api-version]  
    Content-Type: application/json  
    api-key: [admin key]  
```  
A  **api-key** deve ser uma chave de administrador (conforme oposto a uma chave de consulta). Consulte a seção de autenticação em [Segurança do Azure Search](search-security-overview.md) para saber mais sobre as chaves. [Criar um serviço do Azure Search no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

Como alternativa, você pode usar PUT e especificar o nome da fonte de dados  no URI. Se a fonte de dados não existir, ela será criada.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
A **api-version** é necessária. A versão atual é `2016-09-01`. Consulte [versões de API no Azure Search](search-api-versions.md) para obter detalhes.

Para obter orientação sobre a criação de indexadores específicos de plataforma de dados, inicie com [Visão geral de indexadores](search-indexer-overview.md), que inclui a lista completa de [artigos relacionados](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  O número máximo de indexadores permitidos varia por tipo de preço. O serviço gratuito permite até três indexadores. O serviço padrão permite 50 indexadores. Veja [Limites de Serviço](search-limits-quotas-capacity.md) para obter detalhes.    

## <a name="request"></a>Solicitação  
 O corpo da solicitação contém uma definição de indexador, que especifica a fonte de dados e o índice de destino para indexação, bem como a agenda e parâmetros de indexação opcionais.  

 A sintaxe para estruturar a carga da solicitação é indicada a seguir. Uma solicitação de exemplo é fornecida mais adiante neste tópico.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "schedule" : { Optional. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```

### <a name="indexer-schedule"></a>Agenda do indexador  
Um indexador pode, também, especificar uma agenda. Se houver uma agenda, o indexador será executado periodicamente segundo a agenda. O agendador é interno; você não pode usar um agendador externo. **A agenda** tem os seguintes atributos: 

-   **interval**: obrigatório. Um valor de duração que especifica o intervalo ou período de execução do indexador. O menor intervalo permitido é de cinco minutos, e o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `"P[nD][T[nH][nM]]".` Exemplos:  `PT15M`para cada 15 minutos,  `PT2H` para cada 2 horas.  

-   **StartTime** - opcional. Uma data/hora, no horário UTC, quando o indexador deve começar a ser executado.  

### <a name="indexer-parameters"></a>Parâmetros do indexador  
 Um indexador pode, opcionalmente, especificar vários parâmetros que afetam seu comportamento. Todos os parâmetros listados a seguir são opcionais.  

-   **maxFailedItems**:  o número de itens que podem não ser indexados antes que a execução de um indexador seja considerada uma falha. O padrão é 0. Informações sobre itens com falha são retornadas pela operação [Obter Status do Indexador &#40;Azure Search Service REST API&#41](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) .  

-   **maxFailedItemsPerBatch**: o número de itens que podem não ser indexados em cada lote antes que a execução de um indexador seja considerada uma falha. O padrão é 0.  

-   **batchSize:** especifica o número de itens lidos da fonte de dados e indexados como um único lote para melhorar o desempenho. O padrão depende do tipo de fonte de dados: 1000 para o SQL do Azure e o Azure Cosmos DB e 10 para o Armazenamento de Blobs do Azure.

### <a name="field-mapping-parameters"></a>Parâmetros de mapeamento de campo

Definições de indexador contêm associações de campo para mapear um campo de origem para um campo de destino em um índice do Azure Search. Há dois tipos de associações dependendo se a transferência de conteúdo segue um caminho direto ou enriquecido:

+ **fieldMappings** são opcionais, aplicados quando os nomes de campo de origem / destino não correspondem, ou quando você deseja especificar uma função.
+ **outputFieldMappings** são necessários se você estiver criando [um pipeline de enriquecimento](cognitive-search-concept-intro.md). Em um pipeline de enriquecimento, o campo de saída é uma construção definida durante o processo de enriquecimento. Por exemplo, o campo de saída pode ser estrutura composta criada durante o enriquecimento de dois campos separados no documento de origem. 

#### <a name="fieldmappings"></a>fieldMappings

No exemplo a seguir, considere uma tabela de origem com um campo `_id`. O Azure Search não permite um nome de campo iniciado com um sublinhado, portanto, o campo deve ser renomeado. Isso pode ser feito usando a propriedade `fieldMappings` do indexador da seguinte maneira:

```json
"fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
```

Você pode especificar vários mapeamentos de campo:

```json
"fieldMappings" : [
    { "sourceFieldName" : "_id", "targetFieldName" : "id" },
    { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" }
]
```

Nomes de campos de origem e destino diferenciam maiúsculas de minúsculas.

Para saber mais sobre cenários onde os mapeamentos de campo são úteis, consulte [Mapeamentos de Campo do Indexador de Pesquisa](https://docs.microsoft.com/azure/search/search-indexer-field-mappings).

#### <a name="outputfieldmappings"></a>outputFieldMappings

Em cenários de pesquisa cognitiva no qual um conjunto de qualificações está associado a um indexador, você deve adicionar `outputFieldMappings` para associar qualquer saída de uma etapa de enriquecimento que fornece conteúdo para um campo de pesquisado no índice.

```json
  "outputFieldMappings" : [
        {
          "sourceFieldName" : "/document/organizations", 
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*", 
          "targetFieldName" : "keyphrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "language",
            "mappingFunction": null
        }      
   ],
```

<a name="FieldMappingFunctions"></a>

#### <a name="field-mapping-functions"></a>Funções de Mapeamento de Campo

Mapeamentos de campo também podem ser usados para transformar os valores de campo de origem usando *funções de mapeamento de campo*. Por exemplo, um valor de cadeia de caracteres arbitrário pode ser codificado em base 64 para que possa ser usado para preencher um campo de chave do documento.

Para saber mais sobre quando e como usar as funções de mapeamento de campo, consulte [Funções de Mapeamento de Campo](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

### <a name="request-body-examples"></a>Solicitar exemplos de corpo  
 O exemplo a seguir cria um indexador que copia dados da tabela referenciada pela fonte de dados `ordersds` para o índice `orders` em uma agenda que se inicia em 1º de janeiro de 12015 UTC e é executado de hora em hora. Cada invocação do indexador será bem-sucedida se não mais do que cinco itens não forem indexados em cada lote e se não mais do que dez itens não forem indexados no total.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

## <a name="response"></a>Response  
 201 Criado para uma solicitação bem-sucedida.  

## <a name="see-also"></a>Consulte também

+ [ Visão geral da pesquisa cognitiva](cognitive-search-concept-intro.md)
+ [Início Rápido: experimente a pesquisa cognitiva](cognitive-search-quickstart-blob.md)
+ [Como mapear campos de mapa](cognitive-search-output-field-mapping.md)
