---
title: Tutorial sobre como pesquisar o JSON no Armazenamento de Blobs do Azure – Azure Search
description: Neste tutorial, saiba como pesquisar dados de Blobs do Azure semiestruturados usando o Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1c8ce14dd3961eff33a54a14c2bd0b27650d8a50
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201340"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Tutorial: Pesquisar dados semi-estruturados no armazenamento de nuvem do Azure

O Azure Search pode indexar matrizes e documentos JSON no armazenamento de blobs do Azure usando um [indexador](search-indexer-overview.md) que faz leitura de dados semiestruturados. Dados semi-estruturados contêm marcas ou marcações que separam o conteúdo dentro dos dados. Eles dividem a diferença entre dados não estruturados, que devem ser totalmente indexados, e dados estruturados formalmente que aderem a um modelo de dados, como um esquema de banco de dados relacional, que pode ser indexado por campo.

Neste tutorial, use as [APIs REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/) e um cliente REST para executar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma fonte de dados do Azure Search para um contêiner de blobs do Azure
> * Criar um índice do Azure Search para conter conteúdo pesquisável
> * Configurar e executar um indexador para ler o contêiner e extrair conteúdo pesquisável do armazenamento de blobs do Azure
> * Pesquisar no índice que você acabou de criar

> [!NOTE]
> Este tutorial se baseia no suporte para matriz JSON, que é um recurso em versão prévia no Azure Search. Ele não está disponível no portal. Por esse motivo, estamos usando a API REST na versão prévia, que fornece esse recurso e uma ferramenta de cliente REST para chamar a API.

## <a name="prerequisites"></a>Pré-requisitos

[Crie um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial.

[Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para conter dados de exemplo.

[Use Postman](https://www.getpostman.com/) ou outro cliente REST para enviar as solicitações. As instruções para configurar uma solicitação HTTP no Postman são fornecidas na próxima seção.

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure uma solicitação HTTP. Se você não estiver familiarizado com essa ferramenta, consulte [Explorar APIs REST do Azure Search usando Postman](search-fiddler.md).

O método de solicitação para cada chamada neste tutorial é "POST". As chaves de cabeçalho são "Content-type" e "api-key". Os valores das chaves de cabeçalho são "application/json" e sua "chave de administrador" (a chave de administrador é um espaço reservado para sua chave primária de pesquisa) respectivamente. O corpo é onde você coloca o conteúdo real de sua chamada. Dependendo do cliente que você está usando, talvez haja algumas variações em como você cria sua consulta, mas essas são as noções básicas.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/postmanoverview.png)

Para as chamadas REST abordadas neste tutorial, sua chave de API de pesquisa é necessária. Você pode encontrar sua chave de API em **Chaves** dentro de seu serviço de pesquisa. Esta chave de API deve estar no cabeçalho de cada chamada à API (substitua “chave de administrador” na captura de tela anterior por ela) que esse tutorial orienta você a fazer. Mantenha a chave, pois você precisará dela para cada chamada.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/keys.png)

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

1. **Baixe [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** e descompacte-o em sua própria pasta. Para este tutorial, os dados são provenientes do [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) convertidos para JSON.

2. Entre no [portal do Azure](https://portal.azure.com), navegue até a conta de armazenamento do Azure, abra o contêiner de **dados** e clique em **Upload**.

3. Clique em **Avançado**, digite "clinical-trials-json" e, em seguida, carregue todos os arquivos JSON baixados.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/clinicalupload.png)

Após o upload ser concluído, os arquivos devem aparecer em sua própria subpasta dentro do contêiner de dados.

## <a name="connect-your-search-service-to-your-container"></a>Conecte o serviço de pesquisa ao seu contêiner

Estamos usando o Postman fazer três chamadas à API para o serviço de pesquisa para criar uma fonte de dados, um índice e um indexador. A fonte de dados inclui um ponteiro para sua conta de armazenamento e seus dados JSON. O serviço de pesquisa faz a conexão ao carregar os dados.

A cadeia de caracteres deve conter uma API de visualização (como **api-version=2017-11-11-Preview**) e cada chamada deve retornar um **201 - Criado**. A versão de API disponível de forma geral ainda não tem a funcionalidade de lidar com Json como jsonArray, no momento, apenas a versão de API de versão prévia tem esse recurso.

Execute as seguintes três chamadas à API do cliente REST.

## <a name="create-a-data-source"></a>Criar uma fonte de dados

Uma fonte de dados é um objeto do Azure Search que especifica quais dados serão indexados.

O ponto de extremidade desta chamada é `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do serviço de pesquisa. Para essa chamada, você precisa do nome da sua conta de armazenamento e da chave de conta de armazenamento. A chave de conta de armazenamento pode ser encontrada no Portal do Azure dentro das **Chaves de Acesso** da sua conta de armazenamento. A localização é mostrada na imagem a seguir:

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/storagekeys.png)

Certifique-se de substituir o `[storage account name]` e o `[storage account key]` no corpo da sua chamada antes de executar a chamada.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Crie um índice
    
A segunda chamada à API cria um índice do Azure Search. Um índice especifica todos os parâmetros e seus atributos.

A URL para esta chamada é `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do serviço de pesquisa.

Primeiro, substitua a URL. Em seguida, copie e cole o código a seguir no corpo e execute a consulta.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>Criar e executar um indexador

Um indexador conecta a fonte de dados, importa dados para o índice de pesquisa de destino e, opcionalmente, fornece um planejamento para automatizar a atualização de dados.

A URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do serviço de pesquisa.

Primeiro, substitua a URL. Em seguida, copie e cole o código a seguir no corpo e envie a solicitação. A solicitação é processada imediatamente. Quando a resposta voltar, você terá um índice que é pesquisável em texto completo.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Pesquisar os arquivos JSON

Agora, você pode emitir consultas no índice. Para essa tarefa, use o [**Gerenciador de pesquisa**](search-explorer.md) no portal.

  ![Pesquisa não estruturada](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Pesquisa de metadados definida pelo usuário

Como antes, os dados podem ser consultados de várias maneiras: pesquisa de texto completo, propriedades do sistema ou metadados definidos pelo usuário. As propriedades do sistema e os metadados definidos pelo usuário podem ser pesquisados com o parâmetro `$select` apenas se forem marcados como **recuperáveis** durante a criação do índice de destino. Os parâmetros no índice não podem ser alterados depois que são criados. No entanto, parâmetros adicionais podem ser adicionados.

Um exemplo de uma consulta básica é `$select=Gender,metadata_storage_size`, que limita o retorno para esses dois parâmetros.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/lastquery.png)

Um exemplo de consulta mais complexa seria `$filter=MinimumAge ge 30 and MaximumAge lt 75`, que retorna apenas resultados em que o parâmetro MinimumAge é maior que ou igual a 30 e o MaximumAge é menor que 75.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/metadatashort.png)

Se você quiser experimentar e tentar algumas consultas adicionais por conta própria, fique à vontade para fazer isso. Saiba que você pode usar operadores lógicos (and, or, not) e operadores de comparação (eq, ne, gt, lt, ge, le). Comparações de cadeia de caracteres diferenciam maiúsculas de minúsculas.

O parâmetro `$filter` funciona somente com metadados que foram marcadas como filtráveis na criação do índice.

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida de fazer a limpeza depois de um tutorial é excluindo o grupo de recursos que contém o serviço Azure Search. Você pode excluir o grupo de recursos agora para excluir permanentemente todo o conteúdo. No portal, o nome do grupo de recursos está na página Visão geral do serviço Azure Search.

## <a name="next-steps"></a>Próximas etapas

Você pode anexar algoritmos com inteligência artificial a um pipeline do indexador. Como uma próxima etapa, prossiga para o tutorial a seguir.

> [!div class="nextstepaction"]
> [Indexação de documentos no Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)