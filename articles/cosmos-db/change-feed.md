---
title: "Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB | Microsoft Docs"
description: "Use o suporte ao feed de alterações do Azure Cosmos DB para controlar as alterações nos documentos e executar o processamento baseado em eventos como gatilhos e manter os caches e sistemas de análise atualizados."
keywords: "feed de alteração"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 03/23/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: d04d1240fb353a973953b2a90eadc65705219edb
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017


---
<a id="working-with-the-change-feed-support-in-azure-cosmos-db" class="xliff"></a>

# Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB
O [Azure Cosmos DB](../cosmos-db/introduction.md) é um serviço de banco de dados rápido, flexível e replicado globalmente, usado para armazenar grandes volumes de dados transacionais e operacionais com latência previsível de milissegundos de dígito único para leituras e gravações. Isso o torna adequado para IoT, jogos, varejo e aplicativos de log operacional. Um padrão de design comum nesses aplicativos é controlar as alterações feitas nos dados do Azure Cosmos DB e atualizar exibições materializadas, executar análise em tempo real, arquivar dados em armazenamento frio e disparar notificações em determinados eventos de acordo com essas alterações. O **suporte ao feed de alterações** do Azure Cosmos DB permite que você crie soluções eficientes e escalonáveis para cada um desses padrões.

Com suporte ao feed de alterações, o Azure Cosmos DB fornece uma lista classificada de documentos em uma coleção do Azure Cosmos DB na ordem em que eles foram modificados. Este feed pode ser usado para ouvir as modificações de dados dentro da coleção e executar ações como:

* Disparar uma chamada a uma API quando um documento é inserido ou modificado
* Executar o processamento em tempo real (fluxo) em atualizações
* Sincronizar dados com um cache, o mecanismo de pesquisa ou o data warehouse

As alterações no Azure Cosmos DB são persistentes e podem ser processadas de forma assíncrona e distribuídas entre um ou mais consumidores para processamento paralelo. Vamos examinar as APIs do feed de alterações e como usá-las para criar aplicativos escalonáveis em tempo real. Este artigo mostra como trabalhar com os dados espaciais com a API do DocumentDB no Azure Cosmos DB. 

![Usando o feed de alterações do Azure Cosmos DB para capacitar a análise em tempo real e cenários de computação orientada a eventos](./media/change-feed/changefeed.png)

<a id="use-cases-and-scenarios" class="xliff"></a>

## Cenários e casos de uso
O feed de alterações permite o processamento eficiente de grandes conjuntos de dados com um alto volume de gravações e oferece uma alternativa à consulta de conjuntos de dados inteiros para identificar o que foi alterado. Por exemplo, você pode executar as seguintes etapas de forma eficiente:

* Atualize um cache, índice de pesquisa ou data warehouse com os dados armazenados no Azure Cosmos DB.
* Implementar camadas e arquivamento de dados em nível do aplicativo, ou seja, armazenar “dados quentes” no Azure Cosmos DB e arquivar “dados frios” no [Armazenamento de Blobs do Azure](../storage/storage-introduction.md) ou no [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Implementar a análise em lote nos dados usando o [Apache Hadoop](run-hadoop-with-hdinsight.md).
* Implementar [pipelines lambda no Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) com o Azure Cosmos DB. O Azure Cosmos DB fornece uma solução de banco de dados escalonável que pode manipular a ingestão e a consulta, além de implementar arquiteturas lambda com baixo TCO. 
* Realize migrações com zero tempo de inatividade para outra conta do Azure Cosmos DB com um esquema de particionamento diferente.

**Pipelines lambda com o Azure Cosmos DB para ingestão e consulta:**

![Pipeline lambda baseado no Azure Cosmos DB para ingestão e consulta](./media/change-feed/lambda.png)

Use o Azure Cosmos DB para receber e armazenar dados de evento de dispositivos, sensores, infraestrutura e aplicativos, além de processar esses eventos em tempo real com o [Stream Analytics do Azure](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) ou [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

Em aplicativos Web e móveis, você pode acompanhar eventos como alterações no perfil, nas preferências ou no local do cliente para disparar determinadas ações, como enviar notificações para seus dispositivos usando [Azure Functions](../azure-functions/functions-bindings-documentdb.md) ou os [Serviços de Aplicativos](https://azure.microsoft.com/services/app-service/). Se você estiver usando o Azure Cosmos DB para criar um jogo, poderá, por exemplo, usar o feed de alterações para implementar placares em tempo real de acordo com as pontuações dos jogos concluídos.

<a id="how-change-feed-works-in-azure-cosmos-db" class="xliff"></a>

## Como funciona o feed de alterações no Azure Cosmos DB
O Azure Cosmos DB fornece a capacidade de ler de forma incremental as atualizações feitas em uma coleção do Azure Cosmos DB. Este feed de alteração tem as seguintes propriedades:

* As alterações são persistentes no Azure Cosmos DB e podem ser processadas de forma assíncrona.
* As alterações em documentos em uma coleção ficam imediatamente disponíveis no feed de alteração.
* Cada alteração em um documento é exibida apenas uma vez no feed de alteração. Somente a alteração mais recente de um determinado documento é incluída no log de alterações. As alterações intermediárias podem não estar disponíveis.
* O feed de alteração é classificado por ordem de modificação em cada valor de chave de partição. Não há nenhuma garantia de ordem entre os valores de chave de partição.
* As alterações podem ser sincronizadas de qualquer ponto no tempo, ou seja, não há nenhum período de retenção de dados fixo para o qual haja alterações disponíveis.
* As alterações estão disponíveis em blocos de intervalos de chaves de partição. Esse recurso permite que as alterações de coleções grandes sejam processadas em paralelo por vários consumidores/servidores.
* Os aplicativos podem solicitar vários feeds de alteração simultaneamente na mesma coleção.

O feed de alterações do Azure Cosmos DB está habilitado por padrão em todas as contas e não incorre em custos adicionais em sua conta. Use a [produtividade provisionada](request-units.md) em sua região de gravação ou em qualquer [região de leitura](distribute-data-globally.md) para ler o feed de alterações, assim como qualquer outra operação do Azure Cosmos DB. O feed de alteração inclui inserções e as operações de atualização feitas em documentos na coleção. Você pode capturar exclusões ao definir um sinalizador de "exclusão reversível" em seus documentos em vez de exclusões. Como alternativa, você pode definir um período de validade finito para seus documentos por meio do [recurso TTL](time-to-live.md), por exemplo, de 24 horas, e usar o valor dessa propriedade para capturar as exclusões. Com essa solução, você precisa processar alterações em um intervalo de tempo menor do que o período de expiração do TTL. O feed de alteração está disponível para cada intervalo de chaves de partição dentro da coleção de documentos e, portanto, pode ser distribuído entre um ou mais consumidores para processamento paralelo. 

![Processamento distribuído do feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Na próxima seção, descrevemos como acessar o feed de alterações usando a API REST e os SDKs do Azure Cosmos DB. Para aplicativos .NET, recomendamos o uso da [Biblioteca do processador de feed de alterações]() para processar eventos do feed de alterações.

## <a id="rest-apis"></a>Trabalhando com a API REST e o SDK
O Azure Cosmos DB fornece contêineres elásticos de armazenamento e produtividade chamados **coleções**. Os dados em coleções são logicamente agrupados usando [chaves de partição](partition-data.md) para obtenção de escalabilidade e desempenho. O Azure Cosmos DB fornece várias APIs para acessar esses dados, incluindo pesquisa por ID (Read/Get), consulta e feeds de leitura (verificações). O feed de alterações pode ser obtido populando dois novos cabeçalhos de solicitação na API `ReadDocumentFeed` do DocumentDB e pode ser processado em paralelo em intervalos de chaves de partição.

<a id="readdocumentfeed-api" class="xliff"></a>

### API do ReadDocumentFeed
Vamos examinar o funcionamento do ReadDocumentFeed. O Azure Cosmos DB dá suporte à leitura de um feed de documentos em uma coleção por meio da API `ReadDocumentFeed`. Por exemplo, a solicitação a seguir retorna uma página de documentos na coleção `serverlogs`. 

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

Também recupere o feed de documentos usando um dos [SDKs do Azure Cosmos DB](documentdb-sdk-dotnet.md) com suporte. Por exemplo, o trecho a seguir mostra como executar ReadDocumentFeed no .NET.

    FeedResponse<dynamic> feedResponse = null;
    do
    {
        feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
    }
    while (feedResponse.ResponseContinuation != null);

<a id="distributed-execution-of-readdocumentfeed" class="xliff"></a>

### Execução distribuída do ReadDocumentFeed
Para coleções que contêm terabytes de dados ou mais, ou um grande volume de atualizações de ingestão, a execução serial de feed de leitura de um único computador cliente pode não ser prática. Para dar suporte a esses cenários de Big Data, o Azure Cosmos DB fornece APIs para distribuir chamadas `ReadDocumentFeed` de forma transparente entre vários leitores/consumidores cliente. 

**Feed de Documento de Leitura Distribuída**

Para fornecer o processamento escalonável das alterações incrementais, o Azure Cosmos DB dá suporte a um modelo de expansão para a API do feed de alterações de acordo com intervalos de chaves de partição.

* Você pode obter uma lista de intervalos de chaves de partição para uma coleção que esteja executando uma chamada `ReadPartitionKeyRanges`. 
* Para cada intervalo de chaves de partição, você pode realizar uma `ReadDocumentFeed` para ler documentos com chaves de partição dentro do intervalo.

<a id="retrieving-partition-key-ranges-for-a-collection" class="xliff"></a>

### Recuperação de intervalos de chaves de partição para uma coleção
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

Faça isso usando um dos [SDKs do Azure Cosmos DB](documentdb-sdk-dotnet.md) com suporte. Por exemplo, o trecho a seguir mostra como recuperar intervalos de chaves de partição no .NET.

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

O Azure Cosmos DB dá suporte à recuperação de documentos por intervalo de chaves de partição definindo o cabeçalho `x-ms-documentdb-partitionkeyrangeid` opcional. 

<a id="performing-an-incremental-readdocumentfeed" class="xliff"></a>

### Execução de um ReadDocumentFeed incremental
O ReadDocumentFeed dá suporte aos seguintes cenários/tarefas de processamento incremental de alterações em coleções do Azure Cosmos DB:

* Leia todas as alterações para documentos desde o início, ou seja, desde a criação da coleção.
* Leia todas as alterações para atualizações futuras para documentos a partir da hora atual.
* Leia todas as alterações feitas em documentos de uma versão lógica da coleção (ETag). É possível criar pontos de verificação de seus clientes com base em ETag retornado de solicitações de feed de leitura incrementais.

As alterações incluem inserções e atualizações em documentos. Para capturar as exclusões, você deve usar uma propriedade de "exclusão reversível" em documentos ou usar a [propriedade TTL interna](time-to-live.md) para sinalizar uma exclusão pendente no feed de alteração.

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
> Com o feed de alterações, você poderá obter mais itens retornados em uma página que o especificado em `x-ms-max-item-count`, no caso de vários documentos inseridos ou atualizados dentro de procedimentos armazenados ou gatilhos. 

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

E o trecho a seguir mostra como processar alterações em tempo real com o Azure Cosmos DB usando o suporte ao feed de alterações e a função anterior. A primeira chamada retorna todos os documentos na coleção, e o segundo retorna apenas os dois documentos criados desde o último ponto de verificação.

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
A [biblioteca do processador do feed de alterações do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor) pode ser usada para distribuir o processamento de eventos do feed de alterações entre vários consumidores. É necessário usar essa implementação ao criar os leitores do feed de alterações na plataforma .NET. A classe `ChangeFeedProcessorHost` fornece um ambiente de tempo de execução seguro, thread-safe e de vários processos para implementações do processador de eventos, que também fornece gerenciamento de concessão de ponto de verificação e partição.

Para usar a classe [`ChangeFeedProcessorHost`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/ChangeFeedEventHost.cs), é possível implementar [`IChangeFeedObserver`](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/ChangeFeedProcessor/DocumentDB.ChangeFeedProcessor/IChangeFeedObserver.cs). Essa interface contém três métodos:

* OpenAsync
* CloseAsync
* ProcessEventsAsync

Para iniciar o processamento de eventos, crie uma instância do ChangeFeedProcessorHost, fornecendo os parâmetros apropriados para a coleção do Azure Cosmos DB. Em seguida, chame `RegisterObserverAsync` para registrar a implementação `IChangeFeedObserver` no tempo de execução. Neste ponto, o host tentará adquirir uma concessão em todos os intervalos de chaves de partição na coleção do Azure Cosmos DB usando um algoritmo “greedy”. Essas concessões vão durar por determinado período e, em seguida, devem ser renovadas. Como novos nós, as instâncias de trabalho, nesse caso, ficam online, colocam reservas de concessão e com o tempo, a carga é trocada entre os nós, à medida que cada uma tenta adquirir mais concessões.

![Usando o host do processador do feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedprocessor.png)

Ao longo do tempo, é estabelecido um equilíbrio. Essa funcionalidade dinâmica permite que o dimensionamento automático com base em CPU seja aplicado aos consumidores de ampliação e redução de escala. Se as alterações estiverem disponíveis no Azure Cosmos DB a uma taxa mais rápida do que os consumidores podem processar, o aumento de CPU nos consumidores poderá ser usado para causar uma escala automática na contagem de instâncias de trabalho.

A classe `ChangeFeedProcessorHost` também implementa um mecanismo de ponto de verificação usando uma coleção separada de concessões do Azure Cosmos DB. Esse mecanismo armazena o deslocamento por partição, de forma que cada consumidor pode determinar qual foi o último ponto de verificação do consumidor anterior. Conforme as partições fazem transição entre os nós por meio de concessões, esse é o mecanismo de sincronização que facilita a alternância de carga.


Veja a seguir um trecho de código para um host simples do processador de feed de alterações que imprime alterações no console:

```cs
    class DocumentFeedObserver : IChangeFeedObserver
    {
        private static int s_totalDocs = 0;
        public Task OpenAsync(ChangeFeedObserverContext context)
        {
            Console.WriteLine("Worker opened, {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;  // Requires targeting .NET 4.6+.
        }
        public Task CloseAsync(ChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.WriteLine("Worker closed, {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }
        public Task ProcessEventsAsync(IReadOnlyList<Document> docs, ChangeFeedObserverContext context)
        {
            Console.WriteLine("Change feed: total {0} doc(s)", Interlocked.Add(ref s_totalDocs, docs.Count));
            return Task.CompletedTask;
        }
    }
```

O trecho de código a seguir mostra como registrar um novo host para escutar as alterações de uma coleção do Azure Cosmos DB. Aqui, configuramos uma coleção separada para gerenciar as concessões a partições entre vários consumidores:

```cs
    string hostName = Guid.NewGuid().ToString();
    DocumentCollectionInfo documentCollectionLocation = new DocumentCollectionInfo
    {
        Uri = new Uri("https://YOUR_SERVICE.documents.azure.com:443/"),
        MasterKey = "YOUR_SECRET_KEY==",
        DatabaseName = "db1",
        CollectionName = "documents"
    };

    DocumentCollectionInfo leaseCollectionLocation = new DocumentCollectionInfo
    {
        Uri = new Uri("https://YOUR_SERVICE.documents.azure.com:443/"),
        MasterKey = "YOUR_SECRET_KEY==",
        DatabaseName = "db1",
        CollectionName = "leases"
    };

    ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation);
    await host.RegisterObserverAsync<DocumentFeedObserver>();
```

Neste artigo, fornecemos um passo a passo do suporte ao feed de alterações do Azure Cosmos DB e como controlar as alterações feitas nos dados do Azure Cosmos DB usando a API REST e/ou os SDKs. 

<a id="next-steps" class="xliff"></a>

## Próximas etapas
* Experimente as [amostras de código do Feed de alterações do Azure Cosmos DB no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)
* Introdução à codificação com os [SDKs do Azure Cosmos DB](documentdb-sdk-dotnet.md) ou a [API REST](/rest/api/documentdb/)

