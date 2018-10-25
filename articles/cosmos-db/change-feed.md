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
ms.openlocfilehash: b6d05c5e9bc59df9df7ef8840b70ab027b6e2f74
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269489"
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
       
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
            feedProcessorOptions.StartFromBeginning = true;

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

É isso. Após essas etapas, os documentos começarão a aparecer no método **DocumentFeedObserver.ProcessChangesAsync**.

O código acima é para ilustrar os diferentes tipos de objetos e suas interações. Você precisa definir variáveis apropriadas e iniciá-las com valores corretos. É possível obter o código completo usado neste artigo do [repositório do GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> Você nunca deve ter uma chave mestra no código nem no arquivo de configuração, conforme é mostrado no código acima. Confira [como usar o cofre de chaves para recuperar as chaves](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## <a name="faq"></a>Perguntas frequentes

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>Quais são as diferentes maneiras que você pode ler o feed de alterações? E quando usar cada método?

Há três opções para a leitura do feed de alterações:

* **[Usando o SDK do .NET da API do SQL do Azure Cosmos DB](#sql-sdk)**
   
   Usando esse método, você obtém um baixo nível de controle sobre o feed de alterações. Você pode gerenciar o ponto de verificação, pode acessar uma chave de partição específica etc. Se você tiver vários leitores, poderá usar [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) para distribuir a carga de leitura em diferentes threads ou clientes. .

* **[Usando a biblioteca do processador de feed de alterações do Azure Cosmos DB](#change-feed-processor)**

   Se quiser terceirizar grande parte da complexidade do feed de alterações, você poderá usar a biblioteca do processador de feed de alterações. Essa biblioteca oculta grande parte da complexidade, mas ainda fornece a você controle total sobre o feed de alterações. Essa biblioteca segue um [padrão de observador](https://en.wikipedia.org/wiki/Observer_pattern), sua função de processamento é chamada pelo SDK. 

   Se você tiver feed de alterações de alta taxa de transferência, poderá instanciar vários clientes para ler o feed de alterações. Como você está usando a “biblioteca do processador de feed de alterações”, ela dividirá automaticamente a carga entre os diferentes clientes. Você não precisa fazer nada. Toda a complexidade é tratada pelo SDK. No entanto, se quiser ter seu próprio balanceador de carga, você poderá implementar o IParitionLoadBalancingStrategy para a estratégia de partição personalizada. Implementar o IPartitionProcessor: para o processamento personalizado das alterações em uma partição. No entanto, com o SDK, você pode processar um intervalo de partição, mas se quiser processar uma chave de partição específica, precisará usar o SDK para a API do SQL.

* **[Usando o Azure Functions](#azure-functions)** 
   
   A última opção, o Azure Functions, é a mais simples. É recomendável usar essa opção. Quando você cria um gatilho do Azure Cosmos DB em um aplicativo Azure Functions, selecione a coleção do Azure Cosmos DB para conectar-se e a função será disparada sempre que for feita uma alteração à coleção. assistir a uma [transmissão de tela](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) do uso do Azure Functions e do feed de alterações

   Os gatilhos podem ser criados no portal do Azure Functions, no portal do Azure Cosmos DB ou programaticamente. O Visual Studio e o VS Code têm excelente suporte para gravar o Azure Functions. Você pode gravar e depurar o código em sua área de trabalho e, em seguida, implantar a função com um clique. Para obter mais informações, consulte o artigo [Azure Cosmos DB: computação de banco de dados sem servidor usando Azure Functions](serverless-computing-database.md).

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>Qual é a ordem de classificação dos documentos no feed de alterações?

Os documentos do feed de alterações são organizados na ordem da hora da modificação. Essa ordem de classificação é garantida apenas por partição.

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>Para uma conta de várias regiões, o que acontece com o feed de alterações quando a região de gravação passa por failover? O feed de alterações também passa pelo failover? O feed de alterações ainda aparecerá contíguo ou o failover fará com que ele seja reiniciado?

Sim, o feed de alterações funcionará através da operação de failover manual e será contígua.

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>Por quanto tempo o feed de alterações vai manter os dados se eu definir a propriedade TTL (vida útil) do documento como -1?

O feed de alterações persistirá indefinidamente. Se os dados não forem excluídos, eles permanecerão no feed de alterações.

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>Como posso configurar o Azure Functions para ler de uma determinada região, uma vez que o feed de alterações está disponível em todas as regiões de leitura por padrão?

No momento, não é possível configurar o Azure Functions para ler de uma região específica. Há uma emissão do GitHub no repositório do Azure Functions para definir as regiões preferenciais em qualquer associação e gatilho do Azure Cosmos DB.

O Azure Functions usa a política de conexão padrão. Você pode configurar o modo de conexão no Azure Functions e, por padrão, ele lê da região de gravação, portanto, é melhor colocalizar o Azure Functions na mesma região.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Qual é o tamanho padrão dos lotes no Azure Functions?

100 documentos em cada invocação do Azure Functions. No entanto, esse número é configurável no arquivo function.json. Aqui está a [lista de opções de configuração](../azure-functions/functions-run-local.md) completa. Se você estiver desenvolvendo localmente, atualize as configurações de aplicativo dentro do arquivo [local.settings.json](../azure-functions/functions-run-local.md).

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>Estou monitorando uma coleção e lendo seu feed de alterações, no entanto, vejo não estou obtendo todos os documentos inseridos, alguns documentos estão ausentes. O que está acontecendo aqui?

Certifique-se de que não há nenhuma outra função lendo a mesma coleção com a mesma coleção de concessão. Isso aconteceu comigo e, posteriormente, percebi que os documentos ausentes são processados por outros Azure Functions, que também estão usando a mesma concessão.

Portanto, se estiver criando vários Azure Functions para ler o mesmo feed de alterações, eles deverão usar uma coleção de concessão diferente ou usar a configuração “leasePrefix” para compartilhar a mesma coleção. No entanto, quando você usa a biblioteca do processador de feed de alterações, pode iniciar várias instâncias das funções e o SDK dividirá os documentos entre as diferentes instâncias automaticamente para você.

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Meu documento é atualizado a cada segundo e não estou obtendo todas as alterações nas escutas do Azure Functions para o feed de alterações.

O Azure Functions pesquisa o feed de alterações para cada cinco segundos, portanto, quaisquer alterações feitas entre cinco segundos são perdidas. O Azure Cosmos DB armazena apenas uma versão a cada cinco segundos, portanto, você obterá a quinta alteração no documento. No entanto, se quiser ficar abaixo de cinco segundos e pesquisar o feed de alterações a cada segundo, você poderá configurar o tempo de sondagem “feedPollTime”, confira [Associações do Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). Ele é definido em milissegundos com um padrão de 5000. Abaixo de um segundo é possível, mas não é aconselhável, uma vez que você começará a utilizar mais CPU.

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>Inseri um documento na coleção da API do Mongo, mas quando obtenho o documento no feed de alterações, ele mostra um valor de ID diferente. O que há de errado aqui?

A coleção é a coleção da API do Mongo. Lembre-se, o feed de alterações é lido usando o cliente do SQL e serializa os itens no formato JSON. Devido à formatação JSON, clientes do MongoDB enfrentarão uma incompatibilidade entre documentos formatado em BSON e o feed de alterações formatado em JSON. O que você está vendo é a representação de um documento BSON em JSON. Se você usar atributos binários em contas de Mongo, eles são convertidos para o JSON.

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>Há uma maneira de controlar o feed de alterações em relação a atualizações apenas e não inserções?

Atualmente não, mas essa funcionalidade está no roteiro. Atualmente, você pode adicionar um marcador flexível no documento para atualizações.

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>Há uma maneira de obter exclusões no feed de alterações?

No momento, o feed de alterações não registra exclusões. O feed de alterações está em constante aprimoramento e essa funcionalidade está no roteiro. Atualmente, você pode adicionar um marcador flexível no documento para exclusões. Adicione um atributo ao documento chamado “deleted”, e defina-o como “true”, e determine um TTL no documento para que possa ser excluído automaticamente.

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>Posso ler o feed de alterações para documentos históricos (por exemplo, documentos que foram adicionados cinco anos atrás)?

Sim, se o documento não é excluído. você pode ler o feed de alterações do ponto de vista a origem da sua coleção.

### <a name="can-i-read-change-feed-using-javascript"></a>Posso ler o feed de alterações usando JavaScript?

Sim, o suporte inicial do SDK do Node.js para o feed de alterações foi adicionado recentemente. Ele pode ser usado conforme mostrado no exemplo a seguir, atualize o módulo documentdb para a versão atual antes de executar o código:

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
_lsn é uma ID do lote que é adicionada apenas para feed de alterações, representa a ID da transação do repositório. Muitos documentos podem ter o mesmo _lsn.
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
