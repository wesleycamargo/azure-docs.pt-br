---
title: Indexando blobs JSON com o indexador de blobs do Azure Search
description: Indexando blobs JSON com o indexador de blobs do Azure Search
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/07/2017
ms.author: eugenesh
ms.openlocfilehash: 2dac2c5980970946a6b9c26ee6ee8ac0f0344144
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2017
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexando blobs JSON com o indexador de blobs do Azure Search
Este artigo te mostra como configurar o indexador de blob do Azure Search para extrair o conteúdo estruturado de blobs que contêm JSON no armazenamento de Blob do Azure.

Blobs JSON no armazenamento de BLOBs do Azure são normalmente um único documento JSON ou uma matriz JSON. O indexador de blob no Azure Search pode analisar a construção, dependendo de como você definir o parâmetro **parsingMode** na solicitação.

| Documento JSON | parsingMode | Descrição | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por blob | `json` | Analisa blobs JSON como um único bloco de texto. Cada blob JSON torna-se um único documento do Azure Search. | Disponível em ambos os [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) APIs. |
| Múltiplos por blob | `jsonArray` | Analisa uma matriz JSON no blob, onde cada elemento da matriz se torna um documento do Azure Search separado.  | Na versão prévia, em [REST api-version =`2016-09-01-Preview`](search-api-2016-09-01-preview.md) e [.NET SDK visualização](https://aka.ms/search-sdk-preview). |

> [!Note]
> As APIs em visualização servem para teste e avaliação e não devem ser usadas em ambientes de produção.
>

## <a name="setting-up-json-indexing"></a>Configuração da indexação de JSON
Indexação blobs JSON é semelhante para a extração de documento comum em um fluxo de trabalho de três partes comuns a todos os indexadores do Azure Search.

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados

A primeira etapa é prover informações de conexão de fonte de dados usadas pelo indexador. O tipo de fonte de dados, especificado como `azureblob`, determina quais comportamentos de extração de dados são invocados pelo indexador. Para indexação de blob de JSON, a fonte de dados é definição é o mesmo para documentos JSON e matrizes. 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>Etapa 2: Criar um índice de pesquisa de destino 

Indexadores são emparelhados com um esquema de índice. Se você estiver usando a API (em vez do portal), prepare um índice com antecedência para que você pode especificá-lo na operação do indexador. 

> [!Note]
> Indexadores são apresentados no portal por meio de uma ação de **Importação** para um número limitado de indexadores disponíveis. Geralmente, o fluxo de trabalho de importação geralmente pode construir um índice preliminar com base nos metadados na fonte. Para mais informações, veja [Importar dados para o Azure Search no portal](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>Etapa 3: Configurar e executar o indexador

Até agora, as definições para a fonte de dados e o índice foram parsingMode. No entanto, na etapa 3 para a configuração do Indexador, o caminho diverge dependendo de como você deseja que o conteúdo blob JSON a ser analisado e estruturados em um índice do Azure Search.

Ao chamar o indexador, faça o seguinte:

+ Defina o parâmetro **parsingMode** `json`(para indexar cada blob como um único documento) ou `jsonArray` (se seus blobs contêm matrizes JSON e você precisa que cada elemento da matriz seja tratado como um documento separado).

+ Opcionalmente, use **mapeamentos de campo** para selecionar as propriedades do documento JSON de origem usado para preencher o índice de pesquisa de destino. Para matrizes JSON, se existir a matriz como uma propriedade de nível inferior, você pode definir um documento raiz que indica onde a matriz é colocada no blob.

> [!IMPORTANT]
> Ao usar o modo de análise `json` ou `jsonArray`, o Azure Search pressupõe que todos os blobs em sua fonte de dados contêm JSON. Se você precisar dar suporte a uma combinação de blobs JSON e não JSON na mesma fonte de dados, informe-nos em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Como analisar único blobs JSON

Por padrão, o [indexador de blobs do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa blobs JSON como um único bloco de texto. Muitas vezes, você deseja preservar a estrutura dos seus documentos JSON. Por exemplo, suponha que você tem o seguinte documento JSON no armazenamento de BLOBs do Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Definição do indexador para blobs JSON único

Usando o indexador de blob do Azure Search, um documento JSON parecido ao exemplo anterior é analisado em um único documento Azure Search. O indexador carrega um índice por correspondência de "texto", "data de publicação" e "marcas" da origem em relação a campos de destino de forma idêntica nomeadas e digitadas.

Configuração é fornecida no corpo de uma operação do indexador. Lembre-se de que o objeto de fonte de dados, definido previamente, especifica os tipos de informações e a conexão de fonte dados. Além disso, o índice de destino também deve existir como um contêiner vazio em seu serviço. Agenda e parâmetros são opcionais, mas se você omiti-los, o indexador executa imediatamente, usando `json` como o modo de análise.

Uma solicitação totalmente especificada será semelhante ao seguinte:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Conforme observado, os mapeamentos de campo não são necessários. Dado um índice com os campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem um campo de mapeamento presente na solicitação.

## <a name="how-to-parse-json-arrays-preview"></a>Como analisar matrizes JSON (versão prévia)

Como alternativa, você pode escolher para o recurso de visualização de matriz JSON. Essa funcionalidade é útil quando seus blobs contêm uma *matriz de objetos JSON*, e você quer que cada elemento da matriz se torne um documento separado do Azure Search. Por exemplo, dado o blob JSON a seguir, você pode popular o índice do Azure Search com três documentos separados, cada um com os campos "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Definição do indexador para uma matriz JSON

Para uma matriz JSON, a solicitação de indexador usa a API de visualização e o analisador `jsonArray`. Esses são os únicos dois requisitos específicos de matriz para indexação de blobs do JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Novamente, os mapeamentos de campo não são necessários. Dado um índice com os campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem uma lista campo de mapeamento.

### <a name="nested-json-arrays"></a>Matrizes aninhadas de JSON
E se você quiser indexar uma matriz de objetos JSON, mas essa matriz estiver aninhada em algum lugar dentro do documento? Você pode escolher qual propriedade contém a matriz usando a propriedade de configuração `documentRoot` . Por exemplo, se o seu blob tiver esta aparência:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Use esta configuração para indexar a matriz contida na propriedade `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Usando mapeamentos de campo para criar documentos de pesquisa

Quando os campos de origem e destino não estiverem devidamente alinhados, você pode definir uma seção de mapeamento de campo no corpo da solicitação de associações de campo para campo explícitas.

Atualmente, o Azure Search não pode indexar documentos JSON arbitrários diretamente porque dá suporte somente a tipos de dados primitivos, matrizes de cadeia de caracteres e pontos GeoJSON. No entanto, você pode usar **mapeamentos de campo** para separar partes do documento JSON e "elevá-los" para campos de nível superior do documento de pesquisa. Para saber mais sobre conceitos básicos de mapeamentos de campo, consulte [campo mapeamentos de indexadores do Azure Search](search-indexer-field-mappings.md).

Voltando ao nosso documento JSON de exemplo:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Digamos que você tenha um índice de pesquisa com os seguintes campos: `text` do tipo `Edm.String`, `date` do tipo `Edm.DateTimeOffset` e `tags` do tipo `Collection(Edm.String)`. Observe a discrepância entre "data de publicação" na fonte e campo `date` no índice. Para mapear seu JSON para a forma desejada, use os seguintes mapeamentos de campo:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Os nomes dos campos de origem nos mapeamentos são especificados usando a notação [JSON Pointer](http://tools.ietf.org/html/rfc6901) . Comece com uma barra invertida para referir-se à raiz do documento JSON, então selecione a propriedade desejada (em nível arbitrário de aninhamento) usando um caminho separado por barra invertida.

Você também pode se referir a elementos individuais da matriz usando um índice com base em zero. Por exemplo, para selecionar o primeiro elemento da matriz "tags" do exemplo anterior, use um mapeamento de campo como este:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se o nome de um campo de origem em um caminho de mapeamento de campo se referir a uma propriedade que não existe em JSON, esse mapeamento será ignorado sem erro. Isso é feito para que possamos dar suporte a documentos com um esquema diferente (o que é um caso de uso comum). Como não há nenhuma validação, você precisa tomar cuidado para evitar erros de digitação na especificação do mapeamento do campo.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Exemplo: Solicitação de indexador com mapeamentos de campo

O exemplo a seguir é uma carga de indexador totalmente especificada, incluindo mapeamentos de campo:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


## <a name="help-us-make-azure-search-better"></a>Ajude-nos a aprimorar o Azure Search
Se você tiver solicitações de recursos ou ideias para aperfeiçoamentos, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Consulte também

+ [Indexadores no Azure Search](search-indexer-overview.md)
+ [Indexação do Armazenamento de Blobs do Azure com o Azure Search](search-howto-index-json-blobs.md)
+ [Indexando blobs CSV com o indexador de blobs do Azure Search](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquisar dados semi-estruturados do armazenamento de BLOBs do Azure](search-semi-structured-data.md)