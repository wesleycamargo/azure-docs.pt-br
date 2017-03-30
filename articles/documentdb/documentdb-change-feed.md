---
title: "Trabalho com o suporte a Feed de Alteração no Azure DocumentDB | Microsoft Docs"
description: "Use o suporte ao Feed de Alteração do Azure DocumentDB para controlar alterações nos documentos do DocumentDB e executar processamento baseado em eventos como os gatilhos e manter os sistemas de cache e de análise atualizados."
keywords: "feed de alteração"
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 03/20/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 5ad5c688bae7b20ce6e5830e8c7b8dfa9c6df701
ms.lasthandoff: 03/22/2017


---
# <a name="working-with-the-change-feed-support-in-azure-documentdb"></a>Trabalho com o suporte ao Feed de Alteração no Azure DocumentDB
O [Azure DocumentDB](documentdb-introduction.md) é um serviço de banco de dados NoSQL rápido e flexível usado para armazenar grandes volumes de dados transacionais e operacionais com latência previsível de milissegundos de dígito único para leituras e gravações. Isso o torna adequado para IoT, jogos, varejo e aplicativos de log operacional. Um padrão de design comum nesses aplicativos é controlar as alterações feitas nos dados do DocumentDB e atualizar exibições materializadas, executar análise em tempo real, arquivar dados em armazenamento frio e acionar notificações em determinados eventos de acordo com essas alterações. O **suporte ao Feed de Alteração** do DocumentDB permite que você crie soluções eficientes e escalonáveis para cada um desses padrões.

Com suporte ao Feed de Alteração, o DocumentDB fornece uma lista classificada de documentos em uma coleção do DocumentDB na ordem em que eles foram modificados. Este feed pode ser usado para ouvir as modificações de dados dentro da coleção e executar ações como:

* Disparar uma chamada a uma API quando um documento é inserido ou modificado
* Executar o processamento em tempo real (fluxo) em atualizações
* Sincronizar dados com um cache, o mecanismo de pesquisa ou o data warehouse

As alterações no DocumentDB são mantidas e podem ser processadas de forma assíncrona e distribuídas entre um ou mais consumidores para processamento paralelo. Vamos examinar as APIs de Feed de Alteração e como usá-las para criar aplicativos em tempo real escalonáveis.

![Uso do Feed de Alteração do DocumentDB para análise em tempo real e cenários de computação orientada a eventos](./media/documentdb-change-feed/changefeed.png)

## <a name="use-cases-and-scenarios"></a>Cenários e casos de uso
O Feed de Alteração permite processamento eficiente de grandes conjuntos de dados com um alto volume de gravações e oferece uma alternativa ao consultar conjuntos de dados inteiros para identificar o que foi alterado. Por exemplo, você pode executar as seguintes etapas de forma eficiente:

* Atualize um cache, o índice de pesquisa ou um data warehouse com dados armazenados no Azure DocumentDB.
* Implementar dados de nível de aplicativo em camadas e arquivamento, ou seja, armazenar "dados quentes" no DocumentDB e arquivar "dados frios" no [Armazenamento de Blobs do Azure](../storage/storage-introduction.md) ou no [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementar a análise em lote nos dados usando o [Apache Hadoop](documentdb-run-hadoop-with-hdinsight.md).
* Implementar [pipelines lambda no Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) com o DocumentDB. O DocumentDB fornece uma solução de banco de dados dimensionável que pode lidar com a inclusão e a consulta, além de implementar arquiteturas lambda com baixo custo total de propriedade. 
* Realize migrações zero tempo de inatividade para outra conta do Azure DocumentDB com um esquema de particionamento diferente.

**Pipelines lambda com o Azure DocumentDB para ingestão e consulta:**

![Pipeline lambda baseado no Azure DocumentDB para ingestão e consulta](./media/documentdb-change-feed/lambda.png)

Você pode usar o DocumentDB para receber e armazenar dados de evento de dispositivos, sensores, infraestrutura e aplicativos, além de processar esses eventos em tempo real com o [Stream Analytics do Azure](../stream-analytics/stream-analytics-documentdb-output.md), o [Apache Storm](../hdinsight/hdinsight-storm-overview.md) ou o [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

Em aplicativos Web e móveis, você pode acompanhar eventos como alterações no perfil, nas preferências ou no local do cliente para disparar determinadas ações, como enviar notificações para seus dispositivos usando [Azure Functions](../azure-functions/functions-bindings-documentdb.md) ou os [Serviços de Aplicativos](https://azure.microsoft.com/services/app-service/). Se você estiver usando o DocumentDB para criar um jogo, poderá, por exemplo, usar o Feed de Alteração para implementar os placares de líderes em tempo real com base em pontuações de jogos concluídos.

## <a name="how-change-feed-works-in-azure-documentdb"></a>Como funciona o Feed de Alteração no Azure DocumentDB
O DocumentDB oferece a capacidade de ler incrementalmente atualizações feitas em uma coleção do DocumentDB. Este feed de alteração tem as seguintes propriedades:

* As alterações são persistentes no DocumentDB e podem ser processadas de forma assíncrona.
* As alterações em documentos em uma coleção ficam imediatamente disponíveis no feed de alteração.
* Cada alteração em um documento é exibida apenas uma vez no feed de alteração. Somente a alteração mais recente de um determinado documento é incluída no log de alterações. As alterações intermediárias podem não estar disponíveis.
* O feed de alteração é classificado por ordem de modificação em cada valor de chave de partição. Não há nenhuma garantia de ordem entre os valores de chave de partição.
* As alterações podem ser sincronizadas de qualquer ponto no tempo, ou seja, não há nenhum período de retenção de dados fixo para o qual haja alterações disponíveis.
* As alterações estão disponíveis em blocos de intervalos de chaves de partição. Esse recurso permite que as alterações de coleções grandes sejam processadas em paralelo por vários consumidores/servidores.
* Os aplicativos podem solicitar vários Feeds de Alteração simultaneamente na mesma coleção.

O Feed de Alteração do DocumentDB está habilitado por padrão para todas as contas e não incorre em custos adicionais na sua conta. Você pode usar a [taxa de transferência provisionada](documentdb-request-units.md) em sua região de gravação ou qualquer [região de leitura](documentdb-distribute-data-globally.md) para ler o feed de alteração, como em qualquer outra operação do DocumentDB. O feed de alteração inclui inserções e as operações de atualização feitas em documentos na coleção. Você pode capturar exclusões ao definir um sinalizador de "exclusão reversível" em seus documentos em vez de exclusões. Como alternativa, você pode definir um período de validade finito para seus documentos por meio do [recurso TTL](documentdb-time-to-live.md), por exemplo, de 24 horas, e usar o valor dessa propriedade para capturar as exclusões. Com essa solução, você precisa processar alterações em um intervalo de tempo menor do que o período de expiração do TTL. O feed de alteração está disponível para cada intervalo de chaves de partição dentro da coleção de documentos e, portanto, pode ser distribuído entre um ou mais consumidores para processamento paralelo. 

![Processamento distribuído de feed de alteração do DocumentDB](./media/documentdb-change-feed/changefeedvisual.png)

Na seção a seguir, descrevemos como acessar o feed de alteração usando a API de REST e os SDKs do DocumentDB. Para aplicativos .NET, recomendamos o uso da [Biblioteca do processador de feed de alterações]() para processar eventos do feed de alterações.

## <a id="rest-apis"></a>Trabalhando com a API REST e o SDK
O DocumentDB fornece contêineres elásticos de armazenamento e produtividade chamados **coleções**. Os dados em coleções são logicamente agrupados usando [chaves de partição](documentdb-partition-data.md) para obtenção de escalabilidade e desempenho. O DocumentDB fornece várias APIs para acessar esses dados, incluindo pesquisa por ID (Read/Get), consulta e feeds de leitura (verificações). O feed de alteração pode ser obtido por meio da população de dois novos cabeçalhos de solicitação na API `ReadDocumentFeed` do DocumentDB e pode ser processado em paralelo em intervalos de chaves de partição.

### <a name="readdocumentfeed-api"></a>API do ReadDocumentFeed
Vamos examinar o funcionamento do ReadDocumentFeed. O DocumentDB dá suporte à leitura de um feed de documentos em uma coleção por meio da API `ReadDocumentFeed`. Por exemplo, a solicitação a seguir retorna uma página de documentos na coleção `serverlogs`. 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/serverlogs HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

Os resultados podem ser limitados por meio do cabeçalho `x-ms-max-item-count` e as leituras podem ser retomadas reenviando a solicitação com um cabeçalho `x-ms-continuation` retornado na resposta anterior. Quando executado de um único cliente, `ReadDocumentFeed` itera resultados entre partições em série. 

**Feed de Documento de Leitura Serial**

Você também pode recuperar o feed de documentos usando um dos [SDKs do DocumentDB](documentdb-sdk-dotnet.md) com suporte. Por exemplo, o trecho a seguir mostra como executar ReadDocumentFeed no .NET.

    FeedResponse<dynamic> feedResponse = null;
    do
    {
        feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
    }
    while (feedResponse.ResponseContinuation != null);

### <a name="distributed-execution-of-readdocumentfeed"></a>Execução distribuída do ReadDocumentFeed
Para coleções que contêm terabytes de dados ou mais, ou um grande volume de atualizações de ingestão, a execução serial de feed de leitura de um único computador cliente pode não ser prática. Para oferecer suporte a esses cenários de big data, o DocumentDB fornece APIs para distribuir chamadas `ReadDocumentFeed` de forma transparente entre vários leitores/consumidores de cliente. 

**Feed de Documento de Leitura Distribuída**

Para fornecer processamento dimensionável das alterações incrementais, o DocumentDB oferece suporte a um modelo de expansão para a API do feed de alteração com base em intervalos de chaves de partição.

* Você pode obter uma lista de intervalos de chaves de partição para uma coleção que esteja executando uma chamada `ReadPartitionKeyRanges`. 
* Para cada intervalo de chaves de partição, você pode realizar uma `ReadDocumentFeed` para ler documentos com chaves de partição dentro do intervalo.

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>Recuperação de intervalos de chaves de partição para uma coleção
Você pode recuperar os Intervalos de Chaves de Partição solicitando o recurso `pkranges` em uma coleção. Por exemplo, a solicitação a seguir recupera a lista de intervalos de chaves de partição para a coleção `serverlogs`:

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

Essa solicitação retorna a seguinte resposta com metadados sobre os intervalos de chaves de partição:

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**Propriedades do intervalo de chaves de partição**: cada intervalo de chaves de partição inclui as propriedades de metadados na tabela a seguir:

<table>
    <tr>
        <th>Nome do cabeçalho</th>
        <th>Descrição</th>
    </tr>
    <tr>
        <td>ID</td>
        <td>
            <p>A ID para o intervalo de chaves de partição. É uma ID estável e exclusiva dentro de cada coleção.</p>
            <p>Deve ser usada na seguinte chamada para ler as alterações por intervalo de chaves de partição.</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>O valor de hash de chave de partição máxima para o intervalo de chaves de partição. Para uso interno.</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>O valor de hash da chave de partição mínimo para o intervalo de chaves de partição. Para uso interno.</td>
    </tr>        
</table>

Você pode fazer isso usando um dos [SDKs do DocumentDB](documentdb-sdk-dotnet.md) com suporte. Por exemplo, o trecho a seguir mostra como recuperar intervalos de chaves de partição no .NET.

    string pkRangesResponseContinuation = null;
    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

    do
    {
        FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
            collectionUri, 
            new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

        partitionKeyRanges.AddRange(pkRangesResponse);
        pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    }
    while (pkRangesResponseContinuation != null);

O DocumentDB oferece suporte à recuperação de documentos por intervalo de chaves de partição ao definir o cabeçalho `x-ms-documentdb-partitionkeyrangeid` opcional. 

### <a name="performing-an-incremental-readdocumentfeed"></a>Execução de um ReadDocumentFeed incremental
O ReadDocumentFeed dá suporte aos seguintes cenários/tarefas de processamento incremental de alterações em coleções do DocumentDB:

* Leia todas as alterações para documentos desde o início, ou seja, desde a criação da coleção.
* Leia todas as alterações para atualizações futuras para documentos a partir da hora atual.
* Leia todas as alterações feitas em documentos de uma versão lógica da coleção (ETag). É possível criar pontos de verificação de seus clientes com base em ETag retornado de solicitações de feed de leitura incrementais.

As alterações incluem inserções e atualizações em documentos. Para capturar as exclusões, você deve usar uma propriedade de "exclusão reversível" em documentos ou usar a [propriedade TTL interna](documentdb-time-to-live.md) para sinalizar uma exclusão pendente no feed de alteração.

A tabela a seguir lista os cabeçalhos de solicitação e de resposta para operações do ReadDocumentFeed.

**Cabeçalhos de solicitação para ReadDocumentFeed incremental**:

<table>
    <tr>
        <th>Nome do cabeçalho</th>
        <th>Descrição</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>Deve ser definido como "feed Incremental" ou omitido</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>Nenhum cabeçalho: retorna todas as alterações desde o início (criação de coleção)</p>
            <p>"*": retorna todas as novas alterações para dados na coleção</p>
            <p>&lt;etag&gt;: se definida como uma ETag de coleção, retorna todas as alterações feitas desde o carimbo de data/hora lógico</p>
        </td>
    </tr>
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>A ID de intervalo de chaves de partição de leitura de dados.</td>
    </tr>
</table>

**Cabeçalhos de resposta para ReadDocumentFeed incremental**:

<table>
    <tr>
        <th>Nome do cabeçalho</th>
        <th>Descrição</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>O número de sequência lógica (LSN) do último documento retornado na resposta.</p>
            <p>O ReadDocumentFeed incremental pode ser retomado por meio do reenvio desse valor em If-None-Match.</p>
        </td>
    </tr>
</table>

Aqui está um exemplo de solicitação para retornar todas as alterações incrementais na coleção da versão/ETag lógica `28535` e intervalo de chaves de partição = `16`:

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

As alterações são ordenadas por tempo em cada valor de chave de partição dentro do intervalo de chaves de partição. Não há nenhuma garantia de ordem entre os valores de chave de partição. Se houver mais resultados do que pode caber em uma única página, você poderá ler a próxima página de resultados reenviando a solicitação com o cabeçalho `If-None-Match` com valor igual a `etag` da resposta anterior. Se vários documentos tiverem sido inseridos ou atualizados transacionalmente dentro de um procedimento armazenado ou gatilho, serão todos retornados na mesma página de resposta.

> [!NOTE]
> Com a Alteração de Feed, você poderá obter mais itens retornados em uma página que o especificado na `x-ms-max-item-count` no caso de vários documentos inseridos ou atualizados dentro de procedimentos armazenados ou gatilhos. 

O SDK do .NET fornece as classes auxiliares [CreateDocumentChangeFeedQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) e [ChangeFeedOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.changefeedoptions.aspx) para acessar as alterações feitas em uma coleção. O trecho a seguir mostra como recuperar todas as alterações desde o início usando o SDK do .NET de um único cliente.

    private async Task<Dictionary<string, string>> GetChanges(
        DocumentClient client,
        string collection,
        Dictionary<string, string> checkpoints)
    {
        string pkRangesResponseContinuation = null;
        List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();

        do
        {
            FeedResponse<PartitionKeyRange> pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
                collectionUri, 
                new FeedOptions { RequestContinuation = pkRangesResponseContinuation });

            partitionKeyRanges.AddRange(pkRangesResponse);
            pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
        }
        while (pkRangesResponseContinuation != null);

        foreach (PartitionKeyRange pkRange in partitionKeyRanges)
        {
            string continuation = null;
            checkpoints.TryGetValue(pkRange.Id, out continuation);

            IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
                collection,
                new ChangeFeedOptions
                {
                    PartitionKeyRangeId = pkRange.Id,
                    StartFromBeginning = true,
                    RequestContinuation = continuation,
                    MaxItemCount = 1
                });

            while (query.HasMoreResults)
            {
                FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

                foreach (DeviceReading changedDocument in readChangesResponse)
                {
                    Console.WriteLine(changedDocument.Id);
                }

                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
        }

        return checkpoints;
    }

E o trecho a seguir mostra como processar alterações em tempo real com o DocumentDB usando o suporte e a função anterior do Feed de Alteração. A primeira chamada retorna todos os documentos na coleção, e o segundo retorna apenas os dois documentos criados desde o último ponto de verificação.

    // Returns all documents in the collection.
    Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

    // Returns only the two documents created above.
    checkpoints = await GetChanges(client, collection, checkpoints);


Você também pode filtrar o feed de alteração usando lógica de cliente para processar eventos seletivamente. Por exemplo, aqui está um trecho que usa o LINQ no lado do cliente para processar somente os eventos de alteração de temperatura de sensores de dispositivo.

    FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

    foreach (DeviceReading changedDocument in 
        readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
    {
        // trigger an action, like call an API
    }

## <a id="change-feed-processor"></a>Biblioteca do processador de feed de alterações
A [biblioteca do processador de feed de alterações do DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor) pode ser usada para distribuir o processamento de eventos do feed de alterações entre vários consumidores. É necessário usar essa implementação ao criar os leitores do feed de alterações na plataforma .NET. A classe `ChangeFeedProcessorHost` fornece um ambiente de tempo de execução seguro, thread-safe e de vários processos para implementações do processador de eventos, que também fornece gerenciamento de concessão de ponto de verificação e partição.

Para usar a classe [`ChangeFeedProcessorHost`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/ChangeFeedEventHost.cs), é possível implementar [`IChangeFeedObserver`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/IChangeFeedObserver.cs). Essa interface contém três métodos:

* OpenAsync
* CloseAsync
* ProcessEventsAsync

Para iniciar o processamento de eventos, crie uma instância do ChangeFeedProcessorHost, fornecendo os parâmetros apropriados para a coleção do DocumentDB. Em seguida, chame `RegisterObserverAsync` para registrar a implementação `IChangeFeedObserver` no tempo de execução. Neste ponto, o host tentará adquirir uma concessão em todos os intervalos de chaves de partição na coleção do DocumentDB usando um algoritmo “greedy”. Essas concessões vão durar por determinado período e, em seguida, devem ser renovadas. Como novos nós, as instâncias de trabalho, nesse caso, ficam online, colocam reservas de concessão e com o tempo, a carga é trocada entre os nós, à medida que cada uma tenta adquirir mais concessões.

![Usando o host do processador de feed de alterações do DocumentDB](./media/documentdb-change-feed/changefeedprocessor.png)

Ao longo do tempo, é estabelecido um equilíbrio. Essa funcionalidade dinâmica permite que o dimensionamento automático com base em CPU seja aplicado aos consumidores de ampliação e redução de escala. Se as alterações estiverem disponíveis no DocumentDB a uma taxa mais rápida do que os consumidores puderem processar, o aumento de CPU nos consumidores poderá ser usado para causar uma escala automática na contagem de instâncias de trabalho.

A classe ChangeFeedProcessorHost também implementa um mecanismo de ponto de verificação usando uma coleção separada de concessões do DocumentDB. Esse mecanismo armazena o deslocamento por base de partição, de forma que cada consumidor pode determinar qual foi o último ponto de verificação do consumidor anterior. Conforme as partições fazem transição entre os nós por meio de concessões, esse é o mecanismo de sincronização que facilita a alternância de carga.

Neste artigo, fornecemos um passo a passo de suporte do Feed de Alteração do DocumentDB e como controlar as alterações feitas nos dados do DocumentDB usando a API REST e/ou os SDKs do DocumentDB. 

## <a name="next-steps"></a>Próximas etapas
* Experimente os [exemplos de código do Feed de Alteração do DocumentDB no Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)
* Saiba mais sobre o [modelo e a hierarquia de recursos do DocumentDB](documentdb-resources.md)
* Introdução à codificação com os [SDKs do DocumentDB](documentdb-sdk-dotnet.md) ou a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)

