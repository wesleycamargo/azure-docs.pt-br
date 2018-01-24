---
title: Pesquisar dados semi-estruturados no armazenamento de nuvem do Azure
description: Pesquisando dados de blob semi-estruturados usando o Azure Search.
author: roygara
manager: timlt
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: a80ae99c2ada00885019ee93e4ef36821340d3a5
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2018
---
# <a name="part-2-search-semi-structured-data-in-cloud-storage"></a>Parte 2: pesquisar dados semiestruturados no armazenamento de nuvem

Em uma série de tutoriais de duas partes, você aprende como pesquisar dados semiestruturados e não estruturados usando o Azure Search. A [Parte 1](../storage/blobs/storage-unstructured-search.md) abordou a pesquisa em dados não estruturados, mas também incluiu importantes pré-requisitos para este tutorial, como a criação da conta de armazenamento. 

Na Parte 2, o foco passa para os dados semiestruturados, como JSON, armazenados em blobs do Azure. Dados semi-estruturados contêm marcas ou marcações que separam o conteúdo dentro dos dados. Eles dividem a diferença entre dados não estruturados que devem ser indexados holisticamente e dados estruturados formalmente que adotam um modelo de dados, como um esquema de banco de dados relacional, que pode ser rastreado por campo.

Na Parte 2, saiba como:

> [!div class="checklist"]
> * Configurar uma fonte de dados do Azure Search para um contêiner de blobs do Azure
> * Criar e popular um índice e um indexador do Azure Search para rastrear o contêiner e extrair conteúdo pesquisável
> * Pesquisar no índice que você acabou de criar

> [!NOTE]
> Este tutorial se baseia no suporte para matriz JSON, que é um recurso em versão prévia no Azure Search. Ele não está disponível no portal. Por esse motivo, estamos usando a API REST na versão prévia, que fornece esse recurso e uma ferramenta de cliente REST para chamar a API.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [tutorial anterior](../storage/blobs/storage-unstructured-search.md), que fornece a conta de armazenamento e o serviço de pesquisa criados no tutorial anterior.

* Instalação de um cliente REST e compreensão de como construir uma solicitação HTTP. Para os fins desse tutorial, estamos usando o [Postman](https://www.getpostman.com/). Fique à vontade para usar um cliente REST diferente se você já estiver familiarizado com um específico.

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure uma solicitação HTTP. Se não estiver familiarizado com essa ferramenta, consulte [Explorar as APIs REST do Azure Search usando o Fiddler ou Postman](search-fiddler.md) para obter mais informações.

O método de solicitação para cada chamada neste tutorial é "POST". As chaves de cabeçalho são "Content-type" e "api-key". Os valores das chaves de cabeçalho são "application/json" e sua "chave de administrador" (a chave de administrador é um espaço reservado para sua chave primária de pesquisa) respectivamente. O corpo é onde você coloca o conteúdo real de sua chamada. Dependendo do cliente que você está usando, talvez haja algumas variações em como você cria sua consulta, mas essas são as noções básicas.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/postmanoverview.png)

Para as chamadas REST abordadas neste tutorial, sua chave de API de pesquisa é necessária. Você pode encontrar sua chave de API em **Chaves** dentro de seu serviço de pesquisa. Esta chave de API deve estar no cabeçalho de cada chamada à API (substitua “chave de administrador” na captura de tela anterior por ela) que esse tutorial orienta você a fazer. Mantenha a chave, pois você precisará dela para cada chamada.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Baixe os dados de exemplo

Um conjunto de dados de exemplo foi preparado para você. **Baixe [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** e descompacte-o em sua própria pasta.

No exemplo, há arquivos JSON de exemplo, que são originalmente arquivos de texto obtidos de [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Nós os convertemos para JSON para sua conveniência.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no [Portal do Azure](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>Carregar os dados de exemplo

No Portal do Azure, navegue de volta para a conta de armazenamento criada no [tutorial anterior](../storage/blobs/storage-unstructured-search.md). Em seguida, abra o contêiner **data** e clique em **Carregar**.

Clique em **Avançado**, digite "clinical-trials-json" e, em seguida, carregue todos os arquivos JSON baixados.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/clinicalupload.png)

Após o upload ser concluído, os arquivos devem aparecer em sua própria subpasta dentro do contêiner de dados.

## <a name="connect-your-search-service-to-your-container"></a>Conecte o serviço de pesquisa ao seu contêiner

Estamos usando o Postman fazer três chamadas à API para o serviço de pesquisa para criar uma fonte de dados, um índice e um indexador. A fonte de dados inclui um ponteiro para sua conta de armazenamento e seus dados JSON. O serviço de pesquisa faz a conexão ao carregar os dados.

A cadeia de caracteres de consulta deve conter **api-version=2016-09-01-Preview** e cada chamada deve retornar **201 Criado**. A versão de API disponível de forma geral ainda não tem a funcionalidade de lidar com Json como jsonArray, no momento, apenas a versão de API de versão prévia tem esse recurso.

Execute as seguintes três chamadas à API do cliente REST.

### <a name="create-a-datasource"></a>Criar uma fonte de dados

Uma fonte de dados especifica quais dados indexar.

O ponto de extremidade desta chamada é `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do serviço de pesquisa.

Para essa chamada, você precisa do nome da sua conta de armazenamento e da chave de conta de armazenamento. A chave de conta de armazenamento pode ser encontrada no Portal do Azure dentro das **Chaves de Acesso** da sua conta de armazenamento. A localização é mostrada na imagem a seguir:

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

### <a name="create-an-index"></a>Crie um índice
    
A segunda chamada à API cria um índice. Um índice especifica todos os parâmetros e seus atributos.

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

### <a name="create-an-indexer"></a>Criar um indexador

Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e opcionalmente fornece um agendamento para automatizar a atualização de dados.

A URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Substitua `[service name]` pelo nome do serviço de pesquisa.

Primeiro, substitua a URL. Em seguida, copie e cole o código a seguir no corpo e execute a consulta.

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

Agora que o serviço de pesquisa foi conectado a seu contêiner de dados, você pode começar a pesquisar seus arquivos.

Abra o Portal do Azure e navegue de volta para o serviço de pesquisa. Exatamente como você fez no tutorial anterior.

  ![Pesquisa não estruturada](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Pesquisa de metadados definida pelo usuário

Como antes, os dados podem ser consultados de várias maneiras: pesquisa de texto completo, propriedades do sistema ou metadados definidos pelo usuário. As propriedades do sistema e os metadados definidos pelo usuário podem ser pesquisados com o parâmetro `$select` apenas se forem marcados como **recuperáveis** durante a criação do índice de destino. Os parâmetros no índice não podem ser alterados depois que são criados. No entanto, parâmetros adicionais podem ser adicionados.

Um exemplo de uma consulta básica é `$select=Gender,metadata_storage_size`, que limita o retorno para esses dois parâmetros.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/lastquery.png)

Um exemplo de consulta mais complexa seria `$filter=MinimumAge ge 30 and MaximumAge lt 75`, que retorna apenas resultados em que o parâmetro MinimumAge é maior que ou igual a 30 e o MaximumAge é menor que 75.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/metadatashort.png)

Se você quiser experimentar e tentar algumas consultas adicionais por conta própria, fique à vontade para fazer isso. Saiba que você pode usar operadores lógicos (and, or, not) e operadores de comparação (eq, ne, gt, lt, ge, le). Comparações de cadeia de caracteres diferenciam maiúsculas de minúsculas.

O parâmetro `$filter` funciona somente com metadados que foram marcadas como filtráveis na criação do índice.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre a pesquisa de dados semi-estruturados usando o Azure Search, como:

> [!div class="checklist"]
> * Criar um Serviço do Azure Search usando a API REST
> * Usar o Serviço do Azure Search para pesquisar o contêiner

Siga este link para saber mais sobre a pesquisa.

> [!div class="nextstepaction"]
> [Indexação de documentos no Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)