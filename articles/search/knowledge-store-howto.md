---
title: Introdução ao Repositório de Dados de Conhecimento (versão prévia) – Azure Search
description: Conheça as etapas para enviar documentos enriquecidos criados por pipelines de indexação por IA no Azure Search para um repositório de dados de conhecimento em sua conta de armazenamento do Azure. A partir daí, você pode exibir, reformatar e consumir documentos enriquecidos no Azure Search e em outros aplicativos.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 2a904cfb049af413887798c8aab449561bc2b73f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65030044"
---
# <a name="how-to-get-started-with-knowledge-store"></a>Introdução ao Repositório de Dados de Conhecimento

O [Repositório de Dados de Conhecimento](knowledge-store-concept-intro.md) é uma nova versão prévia do recurso no Azure Search que salva os enriquecimentos de IA criados em um pipeline de indexação para mineração de conhecimento em outros aplicativos. Você também pode usar enriquecimentos salvos para entender e refinar um pipeline de indexação do Azure Search.

Um repositório de dados de conhecimento é definido por um conjunto de qualificações. Para cenários comuns de pesquisa de texto completo do Azure Search, o objetivo de um conjunto de qualificações é fornecer enriquecimentos de inteligência artificial para tornar o conteúdo mais pesquisável. Para cenários de repositório de dados de conhecimento, a função de um conjunto de qualificações é criar e preencher várias estruturas de dados para minerar dados de conhecimento.

Neste exercício, comece com dados de exemplo, serviços e ferramentas para aprender o fluxo de trabalho básico para criar e usar seu primeiro repositório de dados de conhecimento, com ênfase na definição do conjunto de qualificações.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste início rápido. 

+ [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial. 

+ [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo. O repositório de dados de conhecimento continuará a existir no armazenamento do Azure.

+ [Criar um recurso de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) na camada pós-paga do S0 para ter acesso alargado a toda a gama de habilidades usadas em enriquecimentos por IA.

+ [Aplicativo da área de trabalho do Postman](https://www.getpostman.com/) para enviar solicitações para o Azure Search.

+ [Coleção do Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/caselaw) com solicitações preparadas para criar uma fonte de dados, índice, conjunto de qualificações e o indexador. Várias definições de objeto são muito longas para serem incluídas neste artigo. Obtenha essa coleção para ver todas as definições do índice e do conjunto de qualificações.

+ [Dados de exemplo do Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) originário da página de download dos Dados Públicos em Massa do [Caselaw Access Project](https://case.law/bulk/download/). Especificamente, o exercício usa os primeiros 10 documentos do primeiro download (Arkansas). Para este exercício, fizemos o download de um exemplo de 10 documentos para o GitHub.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

    ![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-fiddler/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço.

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de Armazenamento do Azure, clique em **Blobs** e, em seguida, clique em **+ Contêiner**.

1. [Crie um contêiner de Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos.

1. Depois que o contêiner for criado, abra-o e selecione **Carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "Upload on command bar")

1. Navegue até a pasta que contém os arquivos de exemplo **caselaw-sample.json**. Escolha o arquivo e, em seguida, clique em **Carregar**.


## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure uma solicitação HTTP. Se você não estiver familiarizado com essa ferramenta, consulte [Explorar APIs REST do Azure Search usando Postman](search-fiddler.md).

+ O método de solicitação para cada chamada neste passo a passo é **POST**.
+ Os cabeçalhos de solicitação (2) incluem o seguinte: "Content-type" definido como "application/json" e "api-key" definido como "chave de administrador" (a chave de administrador é um espaço reservado para sua chave primária de pesquisa) respectivamente. 
+ O corpo da solicitação é onde você coloca o conteúdo real de sua chamada. 

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/postmanoverview.png)

Estamos usando o Postman fazer quatro chamadas à API para o serviço de pesquisa para criar uma fonte de dados, um índice, conjunto de qualificações e um indexador. A fonte de dados inclui um ponteiro para sua conta de armazenamento e seus dados JSON. O serviço de pesquisa faz a conexão ao importar os dados.

[Criar um conjunto de qualificações](#create-skillset) é o foco deste passo a passo: ele especifica as etapas do enriquecimento e como os dados são persistidos em um repositório de dados de conhecimento.

O ponto de extremidade da URL deve especificar uma api-version e cada chamada deve retornar um **201 Criado**. A api-version da versão prévia para criar um conjunto de qualificações com suporte ao repositório de dados de conhecimento é `2019-05-06-Preview`.

Execute as seguintes chamadas à API do cliente REST.

## <a name="create-a-data-source"></a>Criar uma fonte de dados

A [API de Criar Fonte de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) cria um objeto de Azure Search que especifica quais dados indexar.

O ponto de extremidade desta chamada é `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview` 

1. Substitua `[service name]` pelo nome do serviço de pesquisa. 

2. Para essa chamada, o corpo da solicitação deve incluir a cadeia de conexão da conta de armazenamento e o nome do contêiner de blob. A conexão pode ser encontrada no portal do Azure dentro das **Chaves de Acesso** da conta de armazenamento. 

   Lembre-se de substituir a cadeia de conexão e o nome do contêiner de blob no corpo da solicitação antes de executar a chamada.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

3. Enviar a solicitação. A resposta deve ser **201**, e o corpo da resposta deve ser quase idêntico à carga útil da solicitação fornecida.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>Crie um índice
    
A segunda chamada é [API de Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-data-source), criando um índice do Azure Search que armazena todos os dados pesquisáveis. Um índice especifica todos os campos, parâmetros e atributos.

Você não precisa necessariamente de um índice para minerar o conhecimento, mas um indexador não será executado a menos que um índice seja fornecido. 

A URL para esta chamada é `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`

1. Substitua `[service name]` pelo nome do serviço de pesquisa.

2. Copie a definição do índice da solicitação Criar Índice na coleção do Postman no corpo da solicitação. A definição do índice tem centenas de linhas e é longa demais para ser impressa aqui. 

   O shell externo de um índice é formado pelos seguintes elementos. 

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

3. A coleção `fields` contém a maior parte da definição do índice. Ela inclui campos simples, [campos complexos](search-howto-complex-data-types.md) com subestruturas aninhadas e coleções.

   Examine a definição de campo para `casebody` nas linhas 302-384. Observe que um campo complexo poderá conter outros campos complexos quando representações hierárquicas forem necessárias.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

4. Enviar a solicitação. 

   A resposta deve ser **201** e deve ser semelhante ao exemplo a seguir que exibe os primeiros campos de vários campos:

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Criar um conjunto de qualificações e um repositório de conhecimento

A [API Criar Conjunto de Qualificações](https://docs.microsoft.com/rest/api/searchservice/create-skillset) cria um objeto do Azure Search que especifica quais habilidades cognitivas chamar, como interligar habilidades e, o ponto mais importante desse passo a passo, como especificar um repositório de dados de conhecimento.

O ponto de extremidade desta chamada é `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`

1. Substitua `[service name]` pelo nome do serviço de pesquisa.

2. Copie a definição do conjunto de qualificações da solicitação Criar Conjunto de Qualificações na coleção do Postman no corpo da solicitação. A definição do conjunto de qualificações tem centenas de linhas e é longa demais para ser impressa aqui. Porém, esse o foco deste passo a passo.

   O shell externo de um conjunto de qualificações é formado pelos seguintes elementos. A coleção `skills` define enriquecimentos na memória, mas a definição `knowledgeStore` especifica como a saída é armazenada. A definição de `cognitiveServices` é sua conexão aos mecanismos de enriquecimento por IA.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. Primeiro, defina as chaves `cognitiveServices` e `knowledgeStore` e a cadeia de conexão. No exemplo, essas cadeias estão localizadas após a definição do conjunto de qualificações, no final do corpo da solicitação.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "<your cognitive services resource name>",
        "key": "<your cognitive services key>"
    },
    "knowledgeStore": {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your storage account key>;EndpointSuffix=core.windows.net",
    ```

3. Analise o conjunto de qualificações, especialmente as habilidades do Shaper nas linhas 85 e 170, respectivamente. A habilidade do Shaper é importante porque reúne as estruturas de dados que você deseja ter na mineração de dados de conhecimento. Durante a execução da habilidade, essas estruturas estão apenas na memória, mas, à medida que você avança para a próxima etapa, você verá como essa saída pode ser salva em um repositório de dados de conhecimento para poder ser ainda mais explorada.

   O snippet de código a seguir é da linha 207. 

    ```json
    {
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
             ]
          }
     ]
   }
   . . .
   ```

3. Examine o elemento `projections` em `knowledgeStore`, começando na linha 253. As projeções especificam a composição do repositório de dados de conhecimento. As projeções são especificadas em pares de tabelas-objetos, mas, no momento, são especificadas individualmente. Como você pode ver na primeira projeção, `tables` foi especificada, mas `objects` não foi. Na segunda projeção, temos o oposto.

   No armazenamento do Azure, as tabelas serão criadas no armazenamento Tabela para cada tabela criada e cada objeto receberá um contêiner no armazenamento Blob.

   Os objetos geralmente contêm a expressão completa de um enriquecimento. As tabelas geralmente contêm enriquecimentos parciais em combinações organizadas para fins específicos. Este exemplo mostra uma tabela Casos, mas não são mostradas outras tabelas como Entidades, Juízes e Opiniões.

    ```json
    "projections": [
    {
        "tables": [
            {
              "tableName": "Opinions",
              "generatedKeyName": "OpinionId",
              "source": "/document/Case/OpinionsSnippets/*"
            },
          . . . 
        ],
        "objects": []
    },
    {
        "tables": [],
        "objects": [
            {
                "storageContainer": "enrichedcases",
                "key": "/document/CaseFull/Id",
                "source": "/document/CaseFull"
            }
          ]
        }
      ]
    }
    ```

5. Enviar a solicitação. A resposta deve ser **201** e deve ser semelhante ao exemplo a seguir que exibe a primeira parte da resposta.

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>Criar e executar um indexador

A [API Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-indexer) cria e imediatamente executa um indexador. Com esta etapa, todas as definições que você criou até agora são colocadas em funcionamento. O indexador é executado imediatamente já que ele não existe no serviço. Após existir, uma chamada POST para um indexador existente será uma operação de atualização.

O ponto de extremidade desta chamada é `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`

1. Substitua `[service name]` pelo nome do serviço de pesquisa. 

2. No caso dessa chamada, o corpo da solicitação especifica o nome do indexador. O indexador precisa de uma fonte de dados e um índice. Um conjunto de qualificações é opcional para um indexador, mas necessário para enriquecer a Inteligência Artificial.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
                "targetFieldName": "orginizations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
                "targetFieldName": "locations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
                "targetFieldName": "entities",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
                "targetFieldName": "keyPhrases",
                "mappingFunction": null
            }
        ]
    }
    ```

3. Enviar a solicitação. A resposta deve ser **201** e o corpo da resposta deve ser quase idêntico à carga útil da solicitação fornecida (resumida por economizar tempo).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>Explorar o repositório de dados de conhecimento

Comece a explorar assim que o primeiro documento for importado. Para essa tarefa, use o [**Gerenciador de Armazenamento**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) no portal.

É importante perceber que um repositório de dados de conhecimento é totalmente desanexado do Azure Search. O repositório de dados de conhecimento e o índice do Azure Search contêm o conteúdo e a representação de dados, mas separam-se a partir desse ponto. Use o índice na pesquisa de texto completo, pesquisa filtrada e todos os cenários com suporte no Azure Search. Ou siga em frente apenas com o repositório de dados de conhecimento anexando outras ferramentas para analisar o conteúdo.

## <a name="takeaways"></a>Observações

Agora você criou seu primeiro repositório de dados de conhecimento no armazenamento do Azure e usou o Gerenciador de Armazenamento para visualizar os enriquecimentos. Essa é a principal experiência para trabalhar com enriquecimentos armazenados. 

## <a name="next-steps"></a>Próximas etapas

A habilidade do Shaper faz o trabalho pesado na criação de formulários de dados granulares que podem ser combinados em novas formas. Como etapa seguinte, analise a página de referência da habilidade para ver detalhes sobre como ela é usada.

> [!div class="nextstepaction"]
> [Referência da habilidade do Shaper](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->