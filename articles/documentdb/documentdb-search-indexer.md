---
title: "Conectando o DocumentDB à Pesquisa do Azure usando indexadores | Microsoft Docs"
description: Este artigo mostra como usar o indexador da Pesquisa do Azure com o Banco de Dados de Documentos como uma fonte de dados.
services: documentdb
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: mimig
ms.assetid: fdef3d1d-b814-4161-bdb8-e47d29da596f
ms.service: documentdb
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 07/08/2016
ms.author: denlee
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 81dce18eb33dcb31808e41848e543d1488e8cfb7


---
# <a name="connecting-documentdb-with-azure-search-using-indexers"></a>Conectando o Banco de Dados de Documentos à Pesquisa do Azure usando indexadores
Se você está pensando em implementar experiências de pesquisa incríveis para seus dados no Banco de Dados de Documentos, use o indexador da Pesquisa do Azure para o Banco de Dados de Documentos! Neste artigo, mostraremos como integrar o Banco de Dados de Documentos do Azure à Pesquisa do Azure sem precisar escrever nenhum código para manter a infraestrutura de indexação!

Para isso, você precisa [configurar uma conta do Azure Search](../search/search-create-service-portal.md) (não é necessário atualizar para a pesquisa padrão) e chamar a [API REST do Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) para criar uma **fonte de dados** do DocumentDB e um **indexador** para essa fonte de dados.

Para enviar solicitações para interagir com as APIs REST, você pode usar o [Postman](https://www.getpostman.com/), o [Fiddler](http://www.telerik.com/fiddler), ou qualquer outra ferramenta de sua preferência.

## <a name="a-idconceptsaazure-search-indexer-concepts"></a><a id="Concepts"></a>Conceitos do indexador da Pesquisa do Azure
A Pesquisa do Azure dá suporte à criação e ao gerenciamento de fontes de dados (incluindo o Banco de Dados de Documentos) e de indexadores que operam em relação a essas fontes de dados.

Uma **fonte de dados** especifica quais dados precisam ser indexados, as credenciais para acessar os dados e as políticas para habilitar a Pesquisa do Azure a identificar com eficiência as alterações nos dados (como documentos modificados ou excluídos dentro da coleção). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexadores.

Um **indexador** descreve como os dados vão da fonte de dados para o índice de pesquisa de destino. Você deve planejar a criação de um indexador para cada combinação de índice de destino e fonte de dados. Embora você possa ter vários indexadores gravando em um mesmo índice, um indexador pode gravar em apenas um índice. Um indexador é usado para:

* Executar uma cópia única dos dados para preenchimento de um índice.
* Sincronizar um índice com as alterações da fonte de dados segundo uma agenda. A agenda faz parte da definição do indexador.
* Invocar atualizações sob demanda para um índice, conforme necessário.

## <a name="a-idcreatedatasourceastep-1-create-a-data-source"></a><a id="CreateDataSource"></a>Etapa 1: Criar uma fonte de dados
Emita uma solicitação HTTP POST para criar uma nova fonte de dados no serviço de Pesquisa do Azure, incluindo os seguintes cabeçalhos de solicitação.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

A `api-version` é obrigatória. Os valores válidos incluem `2015-02-28` ou uma versão posterior. Visite as [versões de API na Pesquisa do Azure](../search/search-api-versions.md) para ver todas as versões de API da Pesquisa com suporte.

O corpo da solicitação contém a definição da fonte de dados, que deve incluir os seguintes campos:

* **nome**: escolha qualquer nome para representar o banco de dados do Banco de Dados de Documentos.
* **tipo**: Use `documentdb`.
* **credentials**:
  
  * **connectionString**: obrigatório. Especifique as informações de conexão ao banco de dados do Banco de Dados de Documentos do Azure no seguinte formato: `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`
* **container**:
  
  * **name**: obrigatório. Especifique a ID da coleção do Banco de Dados de Documentos a ser indexada.
  * **query**: opcional. Você pode especificar uma consulta para nivelar um documento JSON arbitrário, criando um esquema nivelado que a Pesquisa do Azure pode indexar.
* **dataChangeDetectionPolicy**: opcional. Consulte a [Política de Detecção de Alteração de Dados](#DataChangeDetectionPolicy) abaixo.
* **dataDeletionDetectionPolicy**: opcional. Consulte a [Política de Detecção de Exclusão de Dados](#DataDeletionDetectionPolicy) abaixo.

Veja abaixo um [exemplo de corpo da solicitação](#CreateDataSourceExample).

### <a name="a-iddatachangedetectionpolicyacapturing-changed-documents"></a><a id="DataChangeDetectionPolicy"></a>Capturando documentos alterados
A finalidade de uma política de detecção de alteração de dados é identificar de maneira eficaz dados alterados. Atualmente, a única política com suporte é a política `High Water Mark` que usa a propriedade, que indica o `_ts` carimbo de data/hora da última alteração, fornecida pelo Banco de Dados de Documentos e especificada da seguinte maneira:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Você também precisa adicionar `_ts` à projeção, bem como a cláusula `WHERE` da sua consulta. Por exemplo:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


### <a name="a-iddatadeletiondetectionpolicyacapturing-deleted-documents"></a><a id="DataDeletionDetectionPolicy"></a>Capturando documentos excluídos
Quando linhas são excluídas da tabela de origem, você deve excluí-las também do índice de pesquisa. A finalidade de uma política de detecção de exclusão de dados é identificar de maneira eficaz dados excluídos. Atualmente, a única política com suporte é a política `Soft Delete` (a exclusão recebe algum tipo de marcador), que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [!NOTE]
> Você precisará incluir a propriedade softDeleteColumnName em sua cláusula SELECT se estiver usando uma projeção personalizada.
> 
> 

### <a name="a-idcreatedatasourceexamplearequest-body-example"></a><a id="CreateDataSourceExample"></a>Exemplo de corpo de solicitação
O exemplo a seguir cria uma fonte de dados com uma consulta personalizada e dicas de política:

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

### <a name="response"></a>Resposta
Se a fonte de dados for criada com êxito, você receberá uma resposta de HTTP 201 Criado.

## <a name="a-idcreateindexastep-2-create-an-index"></a><a id="CreateIndex"></a>Etapa 2: Criar um índice
Se ainda não tiver um, crie um índice de destino da Pesquisa do Azure. Você pode fazer isso na [Interface de usuário do Portal do Azure](../search/search-create-index-portal.md) ou usando a [API de Criação de Índices](https://msdn.microsoft.com/library/azure/dn798941.aspx).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Verifique se o esquema do índice de destino é compatível com o esquema dos documentos JSON de origem ou com a saída da projeção de consulta personalizada.

> [!NOTE]
> Para coleções particionadas, a chave do documento padrão é a propriedade `_rid` do DocumentDB, que é renomeada para `rid` no Azure Search. Além disso, os valores `_rid` do DocumentDB contêm caracteres que são inválidos nas chaves do Azure Search; portanto, os valores `_rid` são codificados na Base64.
> 
> 

### <a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Figura A: Mapeamento entre tipos de dados JSON e tipos de dados da Pesquisa do Azure
| TIPO DE DADOS JSON | TIPOS DE CAMPOS DE ÍNDICE DE DESTINO COMPATÍVEIS |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Números que se parecem com inteiros |Edm.Int32, Edm.Int64, Edm.String |
| Números que se parecem com pontos flutuantes |Edm.Double, Edm.String |
| Cadeia de caracteres |Edm.String |
| Matrizes de tipos primitivos, por exemplo, "a", "b", "c" |Collection(Edm.String) |
| Cadeias de caracteres que se parecem com datas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por exemplo, { "type": "Point", "coordinates": [ long, lat ] } |Edm.GeographyPoint |
| Outros objetos JSON |N/D |

### <a name="a-idcreateindexexamplearequest-body-example"></a><a id="CreateIndexExample"></a>Exemplo de corpo de solicitação
O exemplo a seguir cria um índice com um campo de descrição e ID:

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

### <a name="response"></a>Resposta
Se o índice for criado com êxito, você receberá uma resposta de HTTP 201 Criado.

## <a name="a-idcreateindexerastep-3-create-an-indexer"></a><a id="CreateIndexer"></a>Etapa 3: Criar um indexador
Você pode criar um novo indexador em um serviço da Pesquisa do Azure usando uma solicitação HTTP POST com os seguintes cabeçalhos.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

O corpo da solicitação contém a definição do indexador, que deve incluir os seguintes campos:

* **name**: obrigatório. o nome do indexador.
* **dataSourceName**: obrigatório. O nome de uma fonte de dados existente.
* **targetIndexName**: obrigatório. O nome de um índice existente.
* **schedule**: opcional. Consulte [Agenda de indexação](#IndexingSchedule) abaixo.

### <a name="a-idindexingschedulearunning-indexers-on-a-schedule"></a><a id="IndexingSchedule"></a>Executando indexadores de acordo com uma agenda
Um indexador pode, também, especificar uma agenda. Se houver uma agenda, o indexador será executado periodicamente segundo a agenda. A agenda tem os seguintes atributos:

* **interval**: obrigatório. Um valor de duração que especifica o intervalo ou período de execução do indexador. O menor intervalo permitido é de cinco minutos, e o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.
* **startTime**: obrigatório. Uma data/hora, no horário UTC, que especifica quando o indexador deve começar a ser executado.

### <a name="a-idcreateindexerexamplearequest-body-example"></a><a id="CreateIndexerExample"></a>Exemplo de corpo de solicitação
O exemplo a seguir cria um indexador que copia dados da coleção referenciada pela fonte de dados `myDocDbDataSource` para o índice `mySearchIndex` em uma agenda que inicia em 1º de janeiro de 2015 UTC e é executado de hora em hora.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

### <a name="response"></a>Response
Se o indexador for criado com êxito, você receberá uma resposta de HTTP 201 Criado.

## <a name="a-idrunindexerastep-4-run-an-indexer"></a><a id="RunIndexer"></a>Etapa 4: Executar um indexador
Além de ser executado periodicamente segundo uma agenda, o indexador também pode ser invocado sob demanda emitindo a seguinte solicitação HTTP POST:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Resposta
Se o indexador for invocado com êxito, você receberá uma resposta de HTTP 202 Aceito.

## <a name="a-namegetindexerstatusastep-5-get-indexer-status"></a><a name="GetIndexerStatus"></a>Etapa 5: Obter status do indexador
Você pode emitir uma solicitação HTTP GET para recuperar o status atual e o histórico de execução de um indexador:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>Resposta
Você verá uma resposta HTTP 200 OK e um corpo de resposta que contém informações sobre o status geral de integridade, a última invocação e o histórico de invocações recentes do indexador (se houver).

A resposta deve ser semelhante ao seguinte:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

O histórico de execução contém até as 50 execuções mais recentes, que são classificadas em ordem cronológica inversa (de modo que a execução mais recente fique em primeiro lugar).

## <a name="a-namenextstepsanext-steps"></a><a name="NextSteps"></a>Próximas etapas
Parabéns! Você acaba de aprender como integrar o Banco de Dados do Azure à Pesquisa do Azure usando o indexador do Banco de Dados de Documentos.

* Para saber mais sobre o Banco de Dados de Documentos do Azure, confira a [página de serviço do Banco de Dados de Documentos](https://azure.microsoft.com/services/documentdb/).
* Para saber mais sobre a Pesquisa do Azure, confira a [página do serviço de Pesquisa](https://azure.microsoft.com/services/search/).




<!--HONumber=Nov16_HO3-->


