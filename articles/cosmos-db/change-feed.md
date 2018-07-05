---
title: Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB | Microsoft Docs
description: Use o suporte ao feed de alterações do Azure Cosmos DB para controlar as alterações nos documentos e executar o processamento baseado em eventos como gatilhos e manter os caches e sistemas de análise atualizados.
keywords: feed de alteração
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 6b0aaa075b8b2881e269d79a67e75528d0d9a86a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129851"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB

O [Azure Cosmos DB](../cosmos-db/introduction.md) é um banco de dados replicado globalmente de maneira rápida e flexível, adequado para IoT, jogos, varejo e aplicativos de log operacional. Um padrão de design comum nesses aplicativos é usar as alterações nos dados para disparar ações adicionais. Essas ações adicionais podem ser qualquer uma das seguintes: 

* Disparar uma notificação ou uma chamada para uma API quando um documento é inserido ou modificado.
* Transmitir processamento para IoT ou executar a análise.
* Movimentação de dados adicionais sincronizando com um cache, mecanismo de pesquisa ou data warehouse ou arquivando dados para armazenamento frio.

O **suporte ao feed de alterações** do Azure Cosmos DB permite que você crie soluções eficientes e escalonáveis para cada um desses padrões, conforme mostra a imagem a seguir:

![Usando o feed de alterações do Azure Cosmos DB para capacitar a análise em tempo real e cenários de computação orientada a eventos](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Suporte a feed de alterações é fornecido para todos os modelos de dados e contêineres no Azure Cosmos DB. No entanto, o feed de alterações é lido usando o cliente do SQL e serializa os itens no formato JSON. Devido à formatação JSON, clientes do MongoDB enfrentarão uma incompatibilidade entre documentos formatado em BSON e o feed de alterações formatado em JSON.

No vídeo a seguir, o gerente de programa do Azure Cosmos DB, Andrew Liu, demonstra como o feed de alterações do Azure Cosmos DB funciona.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>Como o feed de alterações funciona?

O suporte para feed de alterações no Azure Cosmos DB funciona ouvindo uma coleção do Azure Cosmos DB para quaisquer alterações. Ele gera a lista classificada de documentos que foram alterados na ordem em que eles foram modificados. As alterações são mantidas, podem ser processadas de maneira assíncrona e incremental e a saída pode ser distribuída em um ou mais consumidores para processamento paralelo. 

Você pode ler o feed de alterações de três maneiras diferentes, conforme discutido posteriormente neste artigo:

*   [Usando o Azure Functions](#azure-functions)
*   [Usando o SDK do Azure Cosmos DB](#sql-sdk)
*   [Usando a biblioteca do processador de feed de alterações do Azure Cosmos DB](#change-feed-processor)

O feed de alterações está disponível para cada intervalo de chaves de partição dentro da coleção de documentos e, portanto, pode ser distribuído entre um ou mais consumidores para processamento paralelo, conforme mostra a imagem a seguir.

![Processamento distribuído do feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Detalhes adicionais:
* O feed de alterações é habilitado por padrão para todas as contas.
* Você pode usar a [produtividade provisionada](request-units.md) em sua região de gravação ou em qualquer [região de leitura](distribute-data-globally.md) para ler o feed de alterações, assim como qualquer outra operação do Azure Cosmos DB.
* O feed de alteração inclui inserções e as operações de atualização feitas em documentos na coleção. Você pode capturar exclusões ao definir um sinalizador de "exclusão reversível" em seus documentos em vez de exclusões. Como alternativa, você pode definir um período de validade finito para seus documentos por meio do [recurso TTL](time-to-live.md), por exemplo, de 24 horas, e usar o valor dessa propriedade para capturar as exclusões. Com essa solução, você precisa processar alterações em um intervalo de tempo menor do que o período de expiração do TTL.
* Cada alteração em um documento aparece exatamente uma vez no feed de alterações, e os clientes gerenciam a respectiva lógica do ponto de verificação. A biblioteca do processador do feed de alterações fornece ponto de verificação automático e semântica "pelo menos uma vez".
* Somente a alteração mais recente de um determinado documento é incluída no log de alterações. As alterações intermediárias podem não estar disponíveis.
* O feed de alteração é classificado por ordem de modificação em cada valor de chave de partição. Não há nenhuma garantia de ordem entre os valores de chave de partição.
* As alterações podem ser sincronizadas de qualquer ponto no tempo, ou seja, não há nenhum período de retenção de dados fixo para o qual haja alterações disponíveis.
* As alterações estão disponíveis em blocos de intervalos de chaves de partição. Esse recurso permite que as alterações de coleções grandes sejam processadas em paralelo por vários consumidores/servidores.
* Os aplicativos podem solicitar vários feeds de alterações simultaneamente na mesma coleção.
* ChangeFeedOptions.StartTime pode ser usado para fornecer um ponto de partida inicial, por exemplo, para localizar o token de continuação correspondente a determinada hora. O ContinuationToken, se especificado, supera os valores de StartTime e StartFromBeginning. A precisão de ChangeFeedOptions.StartTime é de aproximadamente 5 segundos. 

## <a name="use-cases-and-scenarios"></a>Cenários e casos de uso

O feed de alterações permite o processamento eficiente de grandes conjuntos de dados com um alto volume de gravações e oferece uma alternativa à consulta de um conjunto de dados inteiro para identificar o que foi alterado. 

Por exemplo, com um feed de alterações, você pode executar as seguintes tarefas com eficiência:

* Atualize um cache, índice de pesquisa ou data warehouse com os dados armazenados no Azure Cosmos DB.
* Implementar camadas e arquivamento de dados em nível do aplicativo, ou seja, armazenar “dados quentes” no Azure Cosmos DB e arquivar “dados frios” no [Armazenamento de Blobs do Azure](../storage/common/storage-introduction.md) ou no [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Realize migrações com zero tempo de inatividade para outra conta do Azure Cosmos DB com um esquema de particionamento diferente.
* Implementar [pipelines lambda no Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) com o Azure Cosmos DB. O Azure Cosmos DB fornece uma solução de banco de dados escalonável que pode manipular a ingestão e a consulta, além de implementar arquiteturas lambda com baixo TCO. 
* Receber e armazenar dados de evento de dispositivos, sensores, infraestrutura e aplicativos, além de processar esses eventos em tempo real com o [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), o [Apache Storm](../hdinsight/storm/apache-storm-overview.md) ou o [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

A imagem a seguir mostra como pipelines lambda que ingerem e consultam usando o Azure Cosmos DB podem usar o suporte para feed de alterações: 

![Pipeline lambda baseado no Azure Cosmos DB para ingestão e consulta](./media/change-feed/lambda.png)

Ainda, em aplicativos Web e móveis [sem servidor](http://azure.com/serverless), você pode acompanhar eventos como alterações no perfil, nas preferências ou no local do cliente para disparar determinadas ações, como enviar notificações por push para seus dispositivos usando o [Azure Functions](#azure-functions). Se você estiver usando o Azure Cosmos DB para criar um jogo, poderá, por exemplo, usar o feed de alterações para implementar placares em tempo real de acordo com as pontuações dos jogos concluídos.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Usando o Azure Functions 

Se você estiver usando o Azure Functions, a maneira mais simples de conectar-se a um feed de alterações do Azure Cosmos DB é adicionar um gatilho do Azure Cosmos DB ao seu aplicativo Azure Functions. Quando você cria um gatilho do Azure Cosmos DB em um aplicativo Azure Functions, selecione a coleção do Azure Cosmos DB para conectar-se e a função será disparada sempre que for feita uma alteração à coleção. 

Os gatilhos podem ser criados no portal do Azure Functions, no portal do Azure Cosmos DB ou programaticamente. Para obter mais informações, consulte [Azure Cosmos DB: computação de banco de dados sem servidor usando Azure Functions](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Usar o SDK

O [SDK do SQL](sql-api-sdk-dotnet.md) para o Azure Cosmos DB fornece todo o poder de ler e gerenciar um feed de alterações. Porém, todo grande poder gera grandes responsabilidades. Se você quiser gerenciar pontos de verificação, lidar com números de sequência de documento e ter controle granular sobre chaves de partição, usar o SDK pode ser a abordagem certa.

Esta seção explica como usar o SDK do SQL para trabalhar com um feed de alterações.

1. Comece lendo os seguintes recursos do appconfig. As instruções sobre como recuperar a chave de autorização e ponto de extremidade estão disponíveis em [Atualizar sua cadeia de conexão](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Crie o cliente da seguinte maneira:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Obter intervalos de chave de partição:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Chame ExecuteNextAsync para cada intervalo de chave de partição:

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

> [!NOTE]
> Em vez de `ChangeFeedOptions.PartitionKeyRangeId`, é possível usar `ChangeFeedOptions.PartitionKey` para especificar uma única chave de partição para a qual obter um feed de alterações. Por exemplo, `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Se você tiver vários leitores, poderá usar **ChangeFeedOptions** para distribuir a carga de leitura em diferentes threads ou clientes.

Isso é tudo, com essas poucas linhas de código, você pode começar a ler o feed de alterações. É possível obter o código completo usado neste artigo do [repositório do GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

No código da etapa 4 acima, o **ResponseContinuation** na última linha tem o último LSN (número de sequência lógica) do documento, que será usado na próxima vez que você ler novos documentos após esse número de sequência. Usando o **StartTime** do **ChangeFeedOption**, você pode ampliar sua rede para obter os documentos. Assim, se a sua **ResponseContinuation** for nula, mas seu **StartTime** voltar no tempo, você obterá todos os documentos que mudaram desde **StartTime**. Mas, se sua **ResponseContinuation** tiver um valor, o sistema lhe enviará todos os documentos desde aquele LSN.

Portanto, sua matriz de ponto de verificação está apenas mantendo o LSN para cada partição. Mas, se você não quiser lidar com as partições, pontos de verificação, LSN, hora de início etc., a opção mais simples será usar a biblioteca do processador de feed de alterações.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Usando a biblioteca do processador de feed de alterações 

A [biblioteca do processador de feed de alterações do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) pode ajudá-lo a facilmente distribuir o processamento de eventos entre vários consumidores. Essa biblioteca simplifica as alterações de leitura em partições e vários threads operando em paralelo.

O principal benefício da biblioteca do processador de feed de alterações é que você não precisa gerenciar cada partição e token de continuação, nem precisa pesquisar cada coleção manualmente.

A biblioteca do processador de feed de alterações simplifica as alterações de leitura em partições e vários threads operando em paralelo.  Ela gerencia automaticamente as alterações de leitura entre partições usando um mecanismo de concessão. Como é possível ver na imagem a seguir, se você iniciar dois clientes que usam a biblioteca do processador de feed de alterações, eles dividirão o trabalho entre si. Enquanto você continua a aumentar os clientes, eles continuam dividindo o trabalho entre si.

![Processamento distribuído do feed de alterações do Azure Cosmos DB](./media/change-feed/change-feed-output.png)

O cliente esquerdo foi iniciado primeiro e ele começou a monitorar todas as partições, então o segundo cliente foi iniciado e, por fim, o primeiro libera algumas das concessões para o segundo cliente. Como você pode ver, essa é a maneira adequada de distribuir o trabalho entre diferentes computadores e clientes.

Observe que, se você tiver duas funções do Azure sem servidor monitorando a mesma coleção e usando a mesma concessão, talvez as duas funções estejam recebendo documentos diferentes, dependendo de como a biblioteca do processador decide processar as partições.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Noções básicas sobre a biblioteca do processador de feed de alterações

Há quatro componentes principais da implementação da biblioteca do processador de feed de alterações: a coleção monitorada, a coleção de concessão, o host de processador e os consumidores. 

> [!WARNING]
> Criar uma coleção tem implicações de preços, pois você está reservando a taxa de transferência para o aplicativo se comunicar com o Azure Cosmos DB. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Coleção monitorada:** a coleção monitorada são os dados dos quais o feed de alterações é gerado. Todas as inserções e alterações à coleção monitorada são refletidas no feed de alterações da coleção. 

**Coleção de concessão:** a coleção de concessão coordena o processamento do feed de alterações em vários trabalhos. Uma coleção separada é usada para armazenar as concessões com uma concessão por partição. É vantajoso armazenar essa coleção de concessão em uma conta diferente, com a região de gravação mais próxima do local em que o processador de feed de alterações está em execução. Um objeto de concessão contém os seguintes atributos: 
* Proprietário: especifica o host que é proprietário da concessão
* Continuação: especifica a posição (token de continuação) de uma determinada partição no feed de alterações
* Carimbo de data/hora: última vez em que a concessão foi atualizada; o carimbo de data/hora pode ser usado para verificar se a concessão está expirada 

**Host de processador:** cada host determina quantas partições devem ser processadas com base no número de instâncias de hosts com concessões ativas. 
1.  Quando um host é iniciado, ele adquire concessões para balancear a carga de trabalho entre todos os hosts. Um host renova concessões periodicamente para que as concessões permaneçam ativas. 
2.  Um host realiza pontos de verificação do último token de continuação em relação à respectiva concessão para cada leitura. Para garantir a segurança de simultaneidade, um host verifica o Etag de cada atualização de concessão. Também há suporte para outras estratégias de ponto de verificação.  
3.  Durante o desligamento, um host libera todas as concessões, mas mantém as informações de continuação, para que possa retomar a leitura do ponto de verificação armazenado mais tarde. 

Neste momento, o número de hosts não pode ser maior que o número de partições (concessões).

**Consumidores:** consumidores ou trabalhos, são os threads que realizam o processamento do feed de alterações iniciado por cada host. Cada host de processador pode ter vários consumidores. Cada consumidor lê o feed de alterações da partição à qual ele é atribuído e notifica o respectivo host sobre as alterações e as concessões expiradas.

Para compreender melhor como esses quatro elementos do processador de feed de alterações funcionam em conjunto, vamos examinar um exemplo no diagrama a seguir. A coleção monitorada armazena documentos e usa a "cidade" como a chave de partição. Podemos ver que a partição azul contém documentos com o campo "cidade" de "A a E" e assim por diante. Há dois hosts, cada um com dois consumidores lendo das quatro partições em paralelo. As setas mostram os consumidores lendo de um ponto específico no feed de alterações. Na primeira partição, o azul mais escuro representa as alterações não lidas enquanto que o azul claro representa as alterações já lidas no feed de alterações. Os hosts de usam a coleção de concessão para armazenar um valor de "continuação" a fim de manter um registro da posição atual de leitura para cada consumidor. 

![Usando o host do processador do feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Trabalhando com a biblioteca do processador de feed de alterações

Antes de instalar o pacote NuGet do processador de feed de alterações, instale: 

* Microsoft.Azure.DocumentDB, versão mais recente.
* Newtonsoft.Json, versão mais recente

Em seguida, instale o [pacote Nuget Microsoft.Azure.DocumentDB.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) e inclua-o como uma referência.

Para implementar a biblioteca do processador de feed de alterações, é necessário fazer o seguinte:

1. Implementar um objeto **DocumentFeedObserver**, que implementa **IChangeFeedObserver**.
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. Implementar um **DocumentFeedObserverFactory**, que implementa um **IChangeFeedObserverFactory**.
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. Defina *CancellationTokenSource* e *ChangeFeedProcessorBuilder*

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. compile o **ChangeFeedProcessorBuilder** após definir os objetos relevantes 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
            ```

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>Posso ler o feed de alterações usando Java?

A biblioteca Java para ler o feed de alterações no [repositório do GitHub](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). No entanto, atualmente a biblioteca Java está algumas versões atrás da biblioteca do .NET. Em breve, ambas as bibliotecas estarão sincronizadas.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Posso usar _etag, _lsn ou _ts para contabilidade interna, que obtenho em resposta?

O formato de _etag é interno e você não deve depender dele (não o analise) porque ele pode ser alterado a qualquer momento.
_ts é o carimbo de hora de criação ou modificação. Você pode usar o _ts para comparação cronológica.
_lsn é uma ID de lote que é adicionada somente para feed de alterações, ele representa a ID de transação do armazenamento. Muitos documentos podem ter o mesmo _lsn.
Mais um ponto a ser observado, ETag no FeedResponse é diferente do _etag visto no documento. _etag é um identificador interno e usado para simultaneidade, ele informa sobre a versão do documento e ETag é usado para o sequenciamento do feed.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Ler um feed de alterações acrescenta custos adicionais?

Você é cobrado pela RU consumida, isto é, movimentação de dados para dentro e para fora das coleções do Azure Cosmos DB, que sempre consomem RUs. Os usuários serão cobrados pelas RUs consumidas pela coleção de concessão.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Vários Azure Functions podem ler o feed de alterações de uma coleção?

Sim. Vários Azure Functions podem ler o mesmo feed de alterações da coleção. No entanto, o Azure Functions precisa ter um leaseCollectionPrefix separado definido.

### <a name="should-the-lease-collection-be-partitioned"></a>A coleção de concessão deve ser particionada?

Não, a coleção de concessão pode ser Fixa. A coleção de concessão particionada não é necessária e não é compatível no momento.

### <a name="can-i-read-change-feed-from-spark"></a>Posse ler o feed de alterações do Spark?

Sim, pode. Consulte o [Conector Spark do Azure Cosmos DB](spark-connector.md). Aqui está uma [transmissão de tela](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s) mostrando como você pode processar o feed de alterações como um fluxo estruturado.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Se eu estiver processando o feed de alterações usando o Azure Functions, por exemplo, um lote de 10 documentos, e obtenho um erro no sétimo documento. Nesse caso, os três últimos documentos não são processados, como posso iniciar o processamento do documento com falha (isto é, sétimo documento) no meu próximo feed?

Para tratar o erro, o padrão recomendado é encapsular seu código com um bloco try-catch. Identifique o erro e coloque o documento em uma fila (mensagens mortas) e, em seguida, defina a lógica para lidar com os documentos que geraram o erro. Com esse método, se você tiver um lote de 200 documentos e apenas um documento falhar, não será necessário descartar o lote inteiro.

No caso de erro, você não deve retroceder o ponto de verificação para o início ou você poderá continuar obtendo os documentos do feed de alterações. Lembre-se de que o feed de alterações mantém o último instantâneo final dos documentos, por causa disso, você pode perder o instantâneo anterior no documento. feed de alterações mantém apenas uma última versão do documento e, no interim, outros processos podem vir e alterar o documento.

Conforme você continua a corrigir o código, logo não encontrará mais documentos na fila de mensagens mortas.
O Azure Functions é chamado automaticamente pelo sistema do feed de alterações e o ponto de verificação etc. é mantido internamente pelo Azure Functions. Se você quiser reverter o ponto de verificação e controlar todos os aspectos dele, deverá considerar o uso do SDK do processador de feed de alterações.


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o Azure Cosmos DB com Azure Functions, consulte [Azure Cosmos DB: computação de banco de dados sem servidor usando o Azure Functions](serverless-computing-database.md).

Para obter mais informações sobre como usar a biblioteca do processador de feed de alterações, use os seguintes recursos:

* [página de informações](sql-api-sdk-dotnet-changefeed.md) 
* [Pacote do Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Código de exemplo mostrando as etapas 1 a 6 acima](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Exemplos adicionais sobre o GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Para obter mais informações sobre como usar o feed de alterações via SDK, use os seguintes recursos:

* [Página de informações do SDK](sql-api-sdk-dotnet.md)
