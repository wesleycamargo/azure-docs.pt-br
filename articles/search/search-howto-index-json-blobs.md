---
title: Indexar blobs JSON no indexador de Blobs do Azure para pesquisa de texto completo – Azure Search
description: Rastrear o conteúdo de texto em blobs JSON do Azure usando o indexador de Blobs do Azure Search. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de blobs do Azure.
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: cafb48f28e38794ce0757d50a5d87432b237e17c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467155"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indexação de blobs JSON com o indexador de blobs do Azure Search
Este artigo te mostra como configurar o indexador de blob do Azure Search para extrair o conteúdo estruturado de blobs que contêm JSON no armazenamento de Blob do Azure.

Você pode usar o [portal](#json-indexer-portal), [APIs REST](#json-indexer-rest), ou [SDK do .NET](#json-indexer-dotnet) para indexar o conteúdo JSON. Comum a todas as abordagens são os documentos JSON localizados em um contêiner de blob em uma conta de Armazenamento do Microsoft Azure. Para obter orientação sobre como enviar documentos JSON por push de outras plataformas não Azure, consulte [Importação de dados no Azure Search](search-what-is-data-import.md).

Blobs JSON no armazenamento de BLOBs do Azure são normalmente um único documento JSON ou uma matriz JSON. O indexador de blob no Azure Search pode analisar a construção, dependendo de como você definir o parâmetro **parsingMode** na solicitação.

> [!IMPORTANT]
> A indexação de blobs JSON está geralmente disponível, mas a análise de JsonArray está em visualização pública e não deve ser usada em ambientes de produção. Para obter mais informações, consulte [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md). 

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Usar o portal

O método mais fácil para indexação de documentos JSON é usar um assistente no [portal do Microsoft Azure](https://portal.azure.com/). Ao analisar metadados no contêiner de blob do Azure, o assistente [ **Importar dados** ](search-import-data-portal.md) pode criar um índice padrão, mapear os campos de origem para campos de índice de destino e carregar o índice em uma única operação. Dependendo do tamanho e complexidade da fonte de dados, é possível ter um índice de pesquisa de texto completo operando em minutos.

### <a name="1---prepare-source-data"></a>1- Preparar dados de origem

É necessário uma conta de armazenamento do Azure com armazenamento de blobs e um contêiner de documentos JSON. Se você estiver familiarizado com qualquer uma dessas tarefas, examine os pré-requisitos que o "Conjunto de backup de dados de exemplo de serviço e o carregamento de serviço Blob do Azure" no [pesquisa cognitiva-quickstart](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o Assistente para Importação de Dados 

Pode-se [iniciar o assistente](search-import-data-portal.md) na barra de comandos na página do serviço Azure Search, ou clicando em **adicionar Azure Search** na seção **serviço Blob** da sua conta de armazenamento no painel esquerdo de navegação.

### <a name="3---set-the-data-source"></a>3 - Configurar a fonte de dados

Na página **fonte de dados**, a fonte deve ser **Armazenamento de blobs do Azure** com as seguintes especificações:

+ Os **Dados para extração** devem ser *Conteúdo e Metadados*. Esta opção permite que ao assistente inferir um esquema de índice e mapear os campos para importação.
   
+ O **Modo de análise** deve ser definido como *JSON* ou *matriz JSON*. 

  O *JSON* articula cada blob como um documento de pesquisa única, aparecendo como um item independente nos resultados da pesquisa. 

  A *Matriz JSON* é para blobs composto de vários elementos, onde cada elemento deve ser articulado como um documento de pesquisa independente autônomo. Se os blobs são complexos e você não escolher *matriz JSON*, o blob inteiro será ingerido como um único documento.
   
+ O **Contêiner de armazenamento** deve especificar sua conta de armazenamento e contêiner ou uma cadeia de caracteres de conexão que apontam para o contêiner. Obtenhas as cadeias de caracteres de conexão na página de portal de serviço Blob.

   ![Definição de fonte de dados de Blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Pular a página de "Adicionar a pesquisa cognitiva" no assistente

Adicionar habilidades cognitivas não é necessário para a importação de documentos JSON. A menos que haja uma necessidade específica de [incluir a API de Serviços Cognitivos e transformações](cognitive-search-concept-intro.md) ao seu pipeline de indexação, ignore esta etapa.

Para ignorar a etapa, clique na página próxima **Personalizar índice de destino**.

### <a name="5---set-index-attributes"></a>5- Definir atributos de índice

Na página **índice**, deve haver uma lista de campos com um tipo de dados e uma série de caixas de seleção para definir os atributos de índice. O assistente pode gerar um índice padrão com base em metadados e pela amostragem dos dados de origem. 

Os padrões geralmente produzem uma solução viável: selecionando um campo (como uma cadeia de caracteres) para servir como a chave ou documento ID para identificar exclusivamente cada documento, bem como os campos que são bons candidatos para pesquisa de texto completo e recuperáveis em um conjunto de resultados do documento. Para blobs, o `content` campo é o melhor candidato para o conteúdo pesquisável.

Você pode aceitar os padrões ou revisar a descrição de [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) para substituir ou complementar os valores iniciais. 

Reserve um tempo para revisar suas seleções. Depois de executar o assistente, estruturas de dados físicos são criadas e você não poderá editar esses campos sem descartar e recriar todos os objetos.

   ![Definição do índice de blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Criar indexador

Totalmente especificado, o assistente cria três objetos diferentes em seu serviço de pesquisa. Um objeto de fonte de dados e um objeto de índice são salvos como recursos nomeados do serviço Azure Search. A última etapa cria um objeto do indexador. Dar um nome ao indexador permite que ele exista como um recurso autônomo, que pode ser agendado e gerenciado independentemente do objeto de fonte de dados e índice criado na mesma sequência do assistente.

Se você não estiver familiarizado com indexadores, um *indexador* é um recurso no Azure Search que rastreia uma fonte de dados externa para o conteúdo pesquisável. A saída do assistente para **Importar dados** é um indexador que rastreia sua fonte de dados JSON, extrai o conteúdo pesquisável e importa-o para um índice no Azure Search.

   ![Definição de indexador de blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Clique em **Ok** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

Pode-se monitorar a importação de dados nas páginas do portal. As notificações de progresso indicam o status da indexação e quantos documentos são carregados. Quando a indexação estiver concluída, você pode usar o [Search explorer](search-explorer.md) para consultar seu índice.

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usar APIs REST

A indexação de blobs JSON é semelhante para a extração de um documento comum em um fluxo de trabalho de três partes comuns a todos os indexadores do Azure Search: criar uma fonte de dados, criar um índice e criar um indexador.

Para a indexação de JSON com base no código, use a API REST com APIs para [indexadores](https://docs.microsoft.com/rest/api/searchservice/create-indexer), [fontes de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) e [índices](https://docs.microsoft.com/rest/api/searchservice/create-index). Em contraste com o assistente do portal, uma abordagem de código requer um índice no local, pronto para aceitar os documentos JSON, quando a solicitação **Criar indexador** for enviada.

Blobs JSON no armazenamento de BLOBs do Azure são normalmente um único documento JSON ou uma matriz JSON. O indexador de blob no Azure Search pode analisar a construção, dependendo de como você definir o parâmetro **parsingMode** na solicitação.

| Documento JSON | parsingMode | DESCRIÇÃO | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por blob | `json` | Analisa blobs JSON como um único bloco de texto. Cada blob JSON torna-se um único documento do Azure Search. | Disponível em ambos os [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) APIs. |
| Múltiplos por blob | `jsonArray` | Analisa uma matriz JSON no blob, onde cada elemento da matriz se torna um documento do Azure Search separado.  | Disponível em ambos os [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) APIs. |


### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados

A primeira etapa é prover informações de conexão de fonte de dados usadas pelo indexador. O tipo de fonte de dados, especificado como `azureblob`, determina quais comportamentos de extração de dados são invocados pelo indexador. Para indexação de BLOBs de JSON, a fonte de dados é definição é o mesmo para documentos JSON e matrizes. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
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

O índice armazena o conteúdo pesquisável no Azure Search. Para criar um índice, forneça um esquema que especifique os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa. Se você criar um índice que tem os mesmos nomes de campo e tipos de dados que a origem, o indexador corresponderá os campos de origem e destino, economizando o trabalho de precisar mapear explicitamente os campos.

O exemplo a seguir mostra uma solicitação de [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index). O índice possuirá um campo `content` pesquisável para armazenar o texto extraído dos blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>Etapa 3: Configurar e executar o indexador

Até agora, as definições para a fonte de dados e o índice foram parsingMode. No entanto, na etapa 3 para a configuração do Indexador, o caminho diverge dependendo de como você deseja que o conteúdo blob JSON a ser analisado e estruturados em um índice do Azure Search. Essas opções incluem `json` ou `jsonArray`:

+ Defina **parsingMode** para `json` para indexar cada blob como um único documento.

+ Defina **parsingMode** para `jsonArray` se seus blobs consistirem em matrizes JSON e precisar que cada elemento da matriz se torne um documento separado no Azure Search. Você pode pensar em um documento como um único item nos resultados da pesquisa. Se quiser que cada elemento na matriz seja exibidos nos resultados da pesquisa como um item independente, use a opção `jsonArray`.

Dentro da definição de indexador, use opcionalmente **mapeamentos de campo** para selecionar as propriedades do documento JSON de origem usado para preencher o índice de pesquisa de destino. Para matrizes JSON, se existir a matriz como uma propriedade de nível inferior, você pode definir um documento raiz que indica onde a matriz é colocada no blob.

> [!IMPORTANT]
> Ao usar o modo de análise `json` ou `jsonArray`, o Azure Search pressupõe que todos os blobs em sua fonte de dados contêm JSON. Se você precisar dar suporte a uma combinação de blobs JSON e não JSON na mesma fonte de dados, informe-nos em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


### <a name="how-to-parse-single-json-blobs"></a>Como analisar único blobs JSON

Por padrão, o [indexador de blobs do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa blobs JSON como um único bloco de texto. Muitas vezes, você deseja preservar a estrutura dos seus documentos JSON. Por exemplo, suponha que você tem o seguinte documento JSON no armazenamento de BLOBs do Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

O indexador de blobs analisa o documento JSON em um único documento do Azure Search. O indexador carrega um índice por correspondência de "texto", "data de publicação" e "marcas" da origem em relação a campos de destino de forma idêntica nomeados e digitados.

Configuração é fornecida no corpo de uma operação do indexador. Lembre-se de que o objeto de fonte de dados, definido previamente, especifica os tipos de informações e a conexão de fonte dados. Além disso, o índice de destino também deve existir como um contêiner vazio em seu serviço. Agenda e parâmetros são opcionais, mas se você omiti-los, o indexador executa imediatamente, usando `json` como o modo de análise.

Uma solicitação totalmente especificada será semelhante ao seguinte:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
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

### <a name="how-to-parse-json-arrays"></a>Como analisar matrizes JSON

Como alternativa, você pode optar pelo recurso de matriz JSON. Essa funcionalidade é útil quando seus blobs contêm uma *matriz de objetos JSON*, e você quer que cada elemento da matriz se torne um documento separado do Azure Search. Por exemplo, dado o blob JSON a seguir, você pode popular o índice do Azure Search com três documentos separados, cada um com os campos "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Para uma matriz JSON, a definição do indexador deve ser semelhante ao exemplo a seguir. Observe que o parâmetro parsingMode especifica o `jsonArray` analisador. Especificar o analisador correto e possuir os dados certos de entrada são os únicos dois requisitos específicos de matriz para indexação de blobs do JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Novamente, os mapeamentos de campo não são necessários. Dado um índice com nomes idênticos nos campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem uma lista campo de mapeamento explícita.

<a name="nested-json-arrays"></a>

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

### <a name="using-field-mappings-to-build-search-documents"></a>Usando mapeamentos de campo para criar documentos de pesquisa

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

Os nomes dos campos de origem nos mapeamentos são especificados usando a notação [JSON Pointer](https://tools.ietf.org/html/rfc6901) . Comece com uma barra invertida para referir-se à raiz do documento JSON, então selecione a propriedade desejada (em nível arbitrário de aninhamento) usando um caminho separado por barra invertida.

Você também pode se referir a elementos individuais da matriz usando um índice com base em zero. Por exemplo, para selecionar o primeiro elemento da matriz "tags" do exemplo anterior, use um mapeamento de campo como este:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se o nome de um campo de origem em um caminho de mapeamento de campo se referir a uma propriedade que não existe em JSON, esse mapeamento será ignorado sem erro. Isso é feito para que possamos dar suporte a documentos com um esquema diferente (o que é um caso de uso comum). Como não há nenhuma validação, você precisa tomar cuidado para evitar erros de digitação na especificação do mapeamento do campo.
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>Exemplo REST: Solicitação de indexador com mapeamentos de campo

O exemplo a seguir é uma carga de indexador totalmente especificada, incluindo mapeamentos de campo:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
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

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Usar o SDK do .NET

O SDK do .NET tem completa paridade com a API REST. É recomendável examinar a seção anterior da API REST para aprender os conceitos, fluxo de trabalho e requisitos. Consulte a seguinte documentação de referência de API do .NET para implementar um indexador JSON em código gerenciado.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>Consulte também

+ [Indexadores no Azure Search](search-indexer-overview.md)
+ [Indexação do Armazenamento de Blobs do Azure com o Azure Search](search-howto-index-json-blobs.md)
+ [Indexando blobs CSV com o indexador de blobs do Azure Search](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquisar dados semiestruturados no Armazenamento de Blobs do Azure](search-semi-structured-data.md)
