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
ms.openlocfilehash: 595502ecf0a78491c73800e8077de65707c7a486
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365178"
---
# <a name="create-indexer-azure-search-service-rest-api-version2017-11-11-preview"></a>Criar Indexador (Azure Search Service REST api-version=2017-11-11-Versão prévia)

Essa referência de API é uma versão específica à versão prévia da documentação, adicionando elementos da [pesquisa cognitiva](cognitive-search-concept-intro.md) à API Criar Indexador. Como com a versão [geralmente disponível](https://docs.microsoft.com/rest/api/searchservice/create-indexer), você pode criar um novo indexador dentro de um serviço do Azure Search usando uma solicitação HTTP POST. 

```http
POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json  
    api-key: [admin key]  
```  
A  **api-key** deve ser uma chave de administrador (conforme oposto a uma chave de consulta). Consulte a seção de autenticação em [Segurança do Azure Search](search-security-overview.md) para saber mais sobre as chaves. [Criar um serviço do Azure Search no portal](search-create-service-portal.md) explica como obter a URL do serviço e as propriedades de chave usadas na solicitação.

Como alternativa, você pode usar PUT e especificar o nome da fonte de dados  no URI. Se a fonte de dados não existir, ela será criada.  

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]  
```  
A **api-version** é necessária. A versão atual disponível é `api-version=2017-11-11`, mas é necessário ter a versão prévia para a pesquisa cognitiva: `api-version=2017-11-11-Preview`.  Consulte [versões de API no Azure Search](search-api-versions.md) para obter detalhes.

Para obter orientação sobre a criação de indexadores específicos de plataforma de dados, inicie com [Visão geral de indexadores](search-indexer-overview.md), que inclui a lista completa de [artigos relacionados](search-indexer-overview.md#next-steps).

> [!NOTE]  
>  O número máximo de indexadores permitidos varia por tipo de preço. O serviço gratuito permite até três indexadores. O serviço padrão permite 50 indexadores. Não há suporte para os serviços de Alta Definição Padrão em indexadores. Veja [Limites de Serviço](search-limits-quotas-capacity.md) para obter detalhes.    

## <a name="request"></a>Solicitação  

Uma [fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source), um [índice](https://docs.microsoft.com/rest/api/searchservice/create-index) e um [conjunto de habilidades](ref-create-skillset.md) fazem parte de uma definição de [indexador](search-indexer-overview.md), mas cada um é um componente independente que pode ser usado em diferentes combinações. Por exemplo, você pode usar a mesma fonte de dados com vários indexadores, o mesmo índice com vários indexadores ou vários indexadores gravando em um único índice.

 O corpo da solicitação contém uma definição de indexador, com as partes a seguir.

+ [dataSourceName](#dataSourceName)
+ [targetIndexName](#targetIndexName)
+ [skillsetName](#skillset)
+ [schedule](#indexer-schedule)
+ [parâmetros](#indexer-parameters)
+ [fieldMappings](#field-mappings)
+ [outputFieldMappings](#output-fieldmappings)

## <a name="request-syntax"></a>Sintaxe da solicitação

A sintaxe para estruturar o conteúdo da solicitação é indicada a seguir. Uma solicitação de exemplo é fornecida mais adiante neste artigo.  

```json
{   
    "name" : "Required for POST, optional for PUT. The name of the indexer",  
    "description" : "Optional. Anything you want, or null",  
    "dataSourceName" : "Required. The name of an existing data source",  
    "targetIndexName" : "Required. The name of an existing index",  
    "skillsetName" : "Required for cognitive search enrichment",
    "schedule" : { Optional, but immediately runs once if unspecified. See Indexing Schedule below. },  
    "parameters" : { Optional. See Indexing Parameters below. },  
    "fieldMappings" : { Optional. See fieldMappings below. },
    "outputFieldMappings" : { Required for enrichment pipelines. See outputFieldMappings below. },
    "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.
}  
```
<a name="dataSourceName"></a>

### <a name="datasourcename"></a>"dataSourceName"

Uma [definição de fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) geralmente inclui propriedades que um indexador pode usar para explorar as características da plataforma de origem. Assim, a fonte de dados passada para o indexador determina a disponibilidade de algumas propriedades e parâmetros, como a filtragem de tipo de conteúdo em blobs do Azure ou o tempo limite de consulta para o Banco de Dados SQL do Azure. 

<a name="targetIndexName"></a>

### <a name="targetindexname"></a>"targetIndexName"

Um [esquema de índice](https://docs.microsoft.com/rest/api/searchservice/create-index) define a coleção de campos que contém campos pesquisáveis, filtráveis, recuperáveis e outras atribuições que determinam como o campo é usado. Durante a indexação, o indexador rastreia a fonte de dados, opcionalmente, desbloqueia documentos e extrai informações, serializa os resultados como JSON e indexa o conteúdo com base no esquema definido para o índice.

<a name="skillset"></a>

### <a name="skillsetname"></a>"skillsetName"

A [pesquisa cognitiva (versão prévia)](cognitive-search-concept-intro.md) refere-se às funcionalidades de processamento de imagens e idioma natural no Azure Search, aplicadas durante a ingestão de dados para extrair entidades, frases-chave, idioma, informações de imagens e assim por diante. As transformações aplicadas ao conteúdo são feitas por meio de *habilidades*, combinadas em um único [*conjunto de habilidades*](ref-create-skillset.md), um por indexador. Assim como ocorre com fontes de dados e índices, um conjunto de habilidades é um componente independente que é anexado a um indexador. Você pode realocar um conjunto de habilidades com outros indexadores, mas cada indexador só pode usar um conjunto de habilidades por vez.
 
<a name="indexer-schedule"></a>

### <a name="schedule"></a>"schedule"  
Um indexador pode, também, especificar uma agenda. Sem um agendamento, o indexador é executado imediatamente quando você envia a solicitação: conectando-se à fonte de dados, rastreando-a e indexando-a. Para alguns cenários, incluindo trabalhos de indexação de execução longa, os agendamentos são usados para [estender a janela de processamento](search-howto-reindex.md#scale-out-indexing) além do máximo de 24 horas. Se houver uma agenda, o indexador será executado periodicamente segundo a agenda. O agendador é interno; você não pode usar um agendador externo. Um **Agendamento** tem os seguintes atributos: 

-   **interval**: obrigatório. Um valor de duração que especifica o intervalo ou período de execução do indexador. O menor intervalo permitido é de cinco minutos; o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `"P[nD][T[nH][nM]]".` Exemplos:  `PT15M`para cada 15 minutos,  `PT2H` para cada 2 horas.  

-   **StartTime** - opcional. Uma data/hora, no horário UTC, quando o indexador deve começar a ser executado.  

<a name="indexer-parameters"></a>

### <a name="parameters"></a>"parameters"

Opcionalmente, um indexador pode usar parâmetros de configuração que modificam os comportamentos do tempo de execução. Os parâmetros de configuração são delimitados por vírgula na solicitação do indexador. 

```json
    {
      "name" : "my-blob-indexer-for-cognitive-search",
      ... other indexer properties
      "parameters" : { "maxFailedItems" : "15", "batchSize" : "100", "configuration" : { "parsingMode" : "json", "indexedFileNameExtensions" : ".json, .jpg, .png", "imageAction" : "generateNormalizedImages", "dataToExtract" : "contentAndMetadata" } }
    }
```

#### <a name="general-parameters-for-all-indexers"></a>Parâmetros gerais para todos os indexadores

| Parâmetro | Tipo e valores permitidos   | Uso  |
|-----------|------------|--------------------------|--------|
| `"batchSize"` | Número inteiro<br/>O padrão é específico à fonte (1.000 para o Banco de Dados SQL do Azure e Azure Cosmos DB, 10 para o Armazenamento de Blobs do Azure) | Especifica o número de itens lidos da fonte de dados e indexados como um único lote para melhorar o desempenho. |
| `"maxFailedItems"` | Número inteiro<br/>O padrão é 0 | Número de erros a serem tolerados antes de uma execução de indexador ser considerada uma falha. Defina essa propriedade como -1 se você não deseja que nenhum erro interrompa o processo de indexação. Recupere as informações sobre os itens com falha usando a operação [Obter Status do Indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).  |
| `"maxFailedItemsPerBatch"` | Número inteiro<br/>O padrão é 0 | Número de erros a serem tolerados em cada lote antes de uma execução de indexador ser considerada uma falha. Defina essa propriedade como -1 se você não deseja que nenhum erro interrompa o processo de indexação. |

#### <a name="blob-configuration-parameters"></a>Parâmetros de configuração de blob

Vários parâmetros são exclusivos a um indexador específico, como a [indexação de blob do Azure](search-howto-indexing-azure-blob-storage.md).

| Parâmetro | Tipo e valores permitidos   | Uso  |
|-----------|---------------------------|--------|
| `"parsingMode"` | Cadeia de caracteres<br/>`"text"`<br/>`"delimitedText"`<br/>`"json"`<br/>`"jsonArray"`  | Para [blobs do Azure](search-howto-indexing-azure-blob-storage.md), defina essa propriedade como `text` para melhorar o desempenho da indexação em arquivos de texto sem formatação no Armazenamento de Blobs. <br/>Para [blobs CSV](search-howto-index-csv-blobs.md), defina essa propriedade como `delimitedText` quando os blobs forem arquivos CSV sem formatação. <br/>Para [blobs JSON](search-howto-index-json-blobs.md), defina essa propriedade como `json` para extrair o conteúdo estruturado ou como `jsonArray` (versão prévia) para extrair elementos individuais de uma matriz como documentos separados no Azure Search. |
| `"excludedFileNameExtensions"` | Cadeia de caracteres<br/>lista delimitada por vírgula | Para [blobs do Azure](search-howto-indexing-azure-blob-storage.md), ignore os tipos de arquivo na lista. Por exemplo, você pode excluir ".png, .png, .mp4" para ignorar os arquivos durante a indexação. | 
| `"indexedFileNameExtensions"` | Cadeia de caracteres<br/>lista delimitada por vírgula | Para [blobs do Azure](search-howto-indexing-azure-blob-storage.md), seleciona os blobs se a extensão de arquivo está na lista. Por exemplo, você pode enfocar a indexação em arquivos de aplicativo específicos ".docx, .pptx, .msg" para incluir especificamente esses tipos de arquivo. | 
| `"failOnUnsupportedContentType"` | verdadeiro <br/>false (padrão) | Para [blobs do Azure](search-howto-indexing-azure-blob-storage.md), defina essa propriedade como `false` se desejar continuar a indexação quando um tipo de conteúdo não compatível for encontrado e você não conhecer todos os tipos de conteúdo (extensões de arquivo) com antecedência. |
| `"failOnUnprocessableDocument"` | verdadeiro <br/>false (padrão)| Para [blobs do Azure](search-howto-indexing-azure-blob-storage.md), defina essa propriedade como `false` se desejar continuar a indexação em caso de falha na indexação de um documento. |
| `"indexStorageMetadataOnlyForOversizedDocuments"` | verdadeiro <br/>false (padrão)| Para [blobs do Azure](search-howto-indexing-azure-blob-storage.md), defina essa propriedade como `true` para ainda indexar os metadados de armazenamento para um conteúdo de blob muito grande para ser processado.  Por padrão, os blobs superdimensionados são tratados como erros. Para obter os limites do tamanho do blob, confira [Limites de serviço](search-limits-quotas-capacity.md). |
| `"delimitedTextHeaders"` | Cadeia de caracteres<br/>lista delimitada por vírgula| Para [blobs CSV (versão prévia)](search-howto-index-csv-blobs.md), especifica uma lista delimitada por vírgula de cabeçalhos de coluna, útil para o mapeamento de campos de origem para os campos de destino em um índice. |
| `"delimitedTextDelimiter"` | Cadeia de caracteres<br/>definido pelo usuário | Para [blobs CSV (versão prévia)](search-howto-index-csv-blobs.md), especifica o delimitador de fim da linha para arquivos CSV, em que cada linha inicia um novo documento (por exemplo, `"|"`).  |
| `"firstLineContainsHeaders"` | true (padrão) <br/>falso | Para [blobs CSV (versão prévia)](search-howto-index-csv-blobs.md), indica que a primeira linha (não vazia) de cada blob contém cabeçalhos.|
| `"documentRoot"`  | Cadeia de caracteres<br/>definido pelo usuário | Para [matrizes JSON (versão prévia)](search-howto-index-json-blobs.md#nested-json-arrays), considerando um documento estruturado ou semiestruturado, especifique um caminho para a matriz usando essa propriedade. |
| `"dataToExtract"` | Cadeia de caracteres<br/>`"storageMetadata"`<br/>`"allMetadata"`<br/>`"contentAndMetadata"` (padrão) | Para [blobs do Azure](search-howto-indexing-azure-blob-storage.md):<br/>Defina essa propriedade como `"storageMetadata"` para indexar apenas as [propriedades de blob padrão e os metadados especificados pelo usuário](../storage/blobs/storage-properties-metadata.md). <br/>Defina essa propriedade como `"allMetadata"` para extrair os metadados fornecidos pelo subsistema de Armazenamento de Blobs do Azure e os [metadados específicos ao tipo de conteúdo](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (por exemplo, metadados exclusivos somente a arquivos .png) são indexados. <br/>Defina essa propriedade como `"contentAndMetadata"` para extrair todos os metadados e o conteúdo textual de cada blob. <br/><br/>Para a [análise de imagem na pesquisa cognitiva (versão prévia)](cognitive-search-concept-image-scenarios.md), quando `"imageAction"` é definido como `"generateNormalizedImages"`, a configuração `"dataToExtract"` informa o indexador de quais dados devem ser extraídos do conteúdo da imagem. Aplica-se ao conteúdo de imagem incorporada em um .PDF ou outro aplicativo ou a arquivos de imagem, como .jpg e .png, em blobs do Azure.  |
| `"imageAction"` |  Cadeia de caracteres<br/>`"none"`<br/>`"generateNormalizedImages"` | Para [blobs do Azure](search-howto-indexing-azure-blob-storage.md), defina essa propriedade como `"none"` para ignorar imagens incorporadas ou arquivos de imagem no conjunto de dados. Esse é o padrão. <br/><br/>Para a [análise de imagem na pesquisa cognitiva](cognitive-search-concept-image-scenarios.md), defina essa propriedade como `"generateNormalizedImages"` para extrair um texto de imagens (por exemplo, a palavra "pare" de uma placa de trânsito "Pare") e inseri-lo como parte do campo de conteúdo. Durante a análise de imagem, o indexador cria uma matriz de imagens normalizadas como parte do desbloqueio de documento e insere as informações geradas no campo de conteúdo. Essa ação exige que `"dataToExtract"` seja definido como `"contentAndMetadata"`. Uma imagem normalizada refere-se ao processamento adicional resultando em uma saída de imagem uniforme, dimensionada e girada para promover uma renderização consistente quando forem incluídas imagens nos resultados da pesquisa visual (por exemplo, fotografias do mesmo tamanho em um controle de grafo, conforme visto na [Demonstração sobre JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Essas informações são geradas para cada imagem quando você usa |


#### <a name="other-configuration-parameters"></a>Outros parâmetros de configuração

Os parâmetros a seguir são específicos ao Banco de Dados SQL do Azure.

| Parâmetro | Tipo e valores permitidos   | Uso  |
|-----------|---------------------------|--------|
| `"queryTimeout"` | Cadeia de caracteres<br/>"hh:mm:ss"<br/>"00:05:00"| Para o [Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), defina esse parâmetro para aumentar o tempo limite além do padrão de 5 minutos.|

<a name="field-mappings"></a>

### <a name="fieldmappings"></a>"fieldMappings"

Definições de indexador contêm associações de campo para mapear um campo de origem para um campo de destino em um índice do Azure Search. Há dois tipos de associações dependendo se a transferência de conteúdo segue um caminho direto ou enriquecido:

+ **fieldMappings** são opcionais, aplicados quando os nomes de campo de origem / destino não correspondem, ou quando você deseja especificar uma função.
+ **outputFieldMappings** são necessários se você estiver criando [um pipeline de enriquecimento](cognitive-search-concept-intro.md). Em um pipeline de enriquecimento, o campo de saída é uma construção definida durante o processo de enriquecimento. Por exemplo, o campo de saída pode ser estrutura composta criada durante o enriquecimento de dois campos separados no documento de origem. 

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

<a name="output-fieldmappings"></a>

### <a name="outputfieldmappings"></a>"outputFieldMappings"

Em cenários de [pesquisa cognitiva](cognitive-search-concept-intro.md) nos quais um conjunto de habilidades está associado a um indexador, é necessário adicionar `outputFieldMappings` para associar qualquer saída de uma etapa de enriquecimento que fornece conteúdo a um campo pesquisável no índice.

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

### <a name="field-mapping-functions"></a>Funções de mapeamento de campo

Mapeamentos de campo também podem ser usados para transformar os valores de campo de origem usando *funções de mapeamento de campo*. Por exemplo, um valor de cadeia de caracteres arbitrário pode ser codificado em base 64 para que possa ser usado para preencher um campo de chave do documento.

Para saber mais sobre quando e como usar as funções de mapeamento de campo, consulte [Funções de Mapeamento de Campo](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#field-mapping-functions).

## <a name="request-examples"></a>Exemplos de solicitação  
 O primeiro exemplo cria um indexador que copia os dados da tabela referenciada pela fonte de dados `ordersds` para o índice `orders`, de acordo com um agendamento que começa em 1º de janeiro de 2015 UTC e é executado de hora em hora. Cada invocação do indexador será bem-sucedida se não mais do que cinco itens não forem indexados em cada lote e se não mais do que dez itens não forem indexados no total.  

```json
{
    "name" : "myindexer",  
    "description" : "a cool indexer",  
    "dataSourceName" : "ordersds",  
    "targetIndexName" : "orders",  
    "schedule" : { "interval" : "PT1H", "startTime" : "2018-01-01T00:00:00Z" },  
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }  
}
```

O segundo exemplo demonstra uma operação de pesquisa cognitiva, indicada pela referência a um conjunto de habilidades e [outputFieldMappings](#output-fieldmappings). [Conjuntos de habilidades](ref-create-skillset.md) são recursos de alto nível, definidos separadamente. Este exemplo é uma abreviação da definição de indexador no [tutorial sobre a pesquisa cognitiva](cognitive-search-tutorial-blob.md).

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
        "sourceFieldName" : "content",
        "targetFieldName" : "content"
    }
   ],
  "outputFieldMappings" : 
  [
    {
        "sourceFieldName" : "/document/organizations", 
        "targetFieldName" : "organizations"
    },
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "configuration": 
    {
    "dataToExtract": "contentAndMetadata",
    "imageAction": "generateNormalizedImages"
    }
  }
}
```

## <a name="response"></a>Response  
 201 Criado para uma solicitação bem-sucedida.  

## <a name="see-also"></a>Consulte também

+ [Visão geral do indexador](search-indexer-overview.md)
+ [ Visão geral da pesquisa cognitiva](cognitive-search-concept-intro.md)
+ [Início Rápido: experimente a pesquisa cognitiva](cognitive-search-quickstart-blob.md)
+ [Como mapear campos de mapa](cognitive-search-output-field-mapping.md)
