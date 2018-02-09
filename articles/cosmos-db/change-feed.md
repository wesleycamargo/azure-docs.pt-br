---
title: "Trabalhando com o suporte ao feed de alterações no Azure Cosmos DB | Microsoft Docs"
description: "Use o suporte ao feed de alterações do Azure Cosmos DB para controlar as alterações nos documentos e executar o processamento baseado em eventos como gatilhos e manter os caches e sistemas de análise atualizados."
keywords: "feed de alteração"
services: cosmos-db
author: rafats
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 01/29/2018
ms.author: rafats
ms.openlocfilehash: 3fa321a3354be3eb7dce2ff886cd40c6c9f1ebbb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
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

1.  [Usando o Azure Functions](#azure-functions)
2.  [Usando o SDK do Azure Cosmos DB](#rest-apis)
3.  [Usando a biblioteca do Processador do feed de alterações do Azure Cosmos DB](#change-feed-processor)

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
* Implementar a análise em lote nos dados usando o [Apache Hadoop](run-hadoop-with-hdinsight.md).
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

<a id="rest-apis"></a>
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

Se você tiver vários leitores, poderá usar **ChangeFeedOptions** para distribuir a carga de leitura em diferentes threads ou clientes.

Isso é tudo, com essas poucas linhas de código, você pode começar a ler o feed de alterações. É possível obter o código completo usado neste artigo do [repositório do GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

No código da etapa 4 acima, o **ResponseContinuation** na última linha tem o último LSN (número de sequência lógica) do documento, que será usado na próxima vez que você ler novos documentos após esse número de sequência. Usando o **StartTime** do **ChangeFeedOption**, você pode ampliar sua rede para obter os documentos. Assim, se a sua **ResponseContinuation** for nula, mas seu **StartTime** voltar no tempo, você obterá todos os documentos que mudaram desde **StartTime**. Mas, se sua **ResponseContinuation** tiver um valor, o sistema lhe enviará todos os documentos desde aquele LSN.

Portanto, sua matriz de ponto de verificação está apenas mantendo o LSN para cada partição. Mas, se você não quiser lidar com as partições, pontos de verificação, LSN, hora de início etc., a opção mais simples será usar a biblioteca do Processador de Feed de Alterações.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Usando a biblioteca do Processador do Feed de Alterações 

A [biblioteca do Processador de Feed de Alterações do Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) pode ajudá-lo a facilmente distribuir o processamento de eventos entre vários consumidores. Essa biblioteca simplifica as alterações de leitura em partições e vários threads operando em paralelo.

O principal benefício da biblioteca do Processador de Feed de Alterações é que você não precisa gerenciar cada partição e token de continuação, nem precisa pesquisar cada coleção manualmente.

A biblioteca do Processador do Feed de Alterações simplifica as alterações de leitura em partições e vários threads operando em paralelo.  Ela gerencia automaticamente as alterações de leitura entre partições usando um mecanismo de concessão. Como você pode ver na imagem a seguir, se você iniciar dois clientes que usam a biblioteca do Processador de Feed de Alterações, eles dividirão o trabalho entre si. Enquanto você continua a aumentar os clientes, eles continuam dividindo o trabalho entre si.

![Processamento distribuído do feed de alterações do Azure Cosmos DB](./media/change-feed/change-feed-output.png)

O cliente esquerdo foi iniciado primeiro e ele começou a monitorar todas as partições, então o segundo cliente foi iniciado e, por fim, o primeiro libera algumas das concessões para o segundo cliente. Como você pode ver, essa é a maneira adequada de distribuir o trabalho entre diferentes computadores e clientes.

Observe que, se você tiver duas funções do Azure sem servidor monitorando a mesma coleção e usando a mesma concessão, talvez as duas funções estejam recebendo documentos diferentes, dependendo de como a biblioteca do processador decide processar as partições.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Noções básicas sobre a biblioteca do Processador do Feed de Alterações

Há quatro componentes principais da implementação do Processador do feed de alterações: a coleção monitorada, a coleção de concessão, o host de processador e os consumidores. 

> [!WARNING]
> Criar uma coleção tem implicações de preços, pois você está reservando a taxa de transferência para o aplicativo se comunicar com o Azure Cosmos DB. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

**Coleção monitorada:** a coleção monitorada são os dados dos quais o feed de alterações é gerado. Todas as inserções e alterações à coleção monitorada são refletidas no feed de alterações da coleção. 

**Coleção de concessão:** a coleção de concessão coordena o processamento do feed de alterações em vários trabalhos. Uma coleção separada é usada para armazenar as concessões com uma concessão por partição. É vantajoso armazenar essa coleção de concessão em uma conta diferente, com a região de gravação mais próxima do local em que o Processador do feed de alterações está em execução. Um objeto de concessão contém os seguintes atributos: 
* Proprietário: especifica o host que é proprietário da concessão
* Continuação: especifica a posição (token de continuação) de uma determinada partição no feed de alterações
* Carimbo de data/hora: última vez em que a concessão foi atualizada; o carimbo de data/hora pode ser usado para verificar se a concessão está expirada 

**Host de processador:** cada host determina quantas partições devem ser processadas com base no número de instâncias de hosts com concessões ativas. 
1.  Quando um host é iniciado, ele adquire concessões para balancear a carga de trabalho entre todos os hosts. Um host renova concessões periodicamente para que as concessões permaneçam ativas. 
2.  Um host realiza pontos de verificação do último token de continuação em relação à respectiva concessão para cada leitura. Para garantir a segurança de simultaneidade, um host verifica o Etag de cada atualização de concessão. Também há suporte para outras estratégias de ponto de verificação.  
3.  Durante o desligamento, um host libera todas as concessões, mas mantém as informações de continuação, para que possa retomar a leitura do ponto de verificação armazenado mais tarde. 

Neste momento, o número de hosts não pode ser maior que o número de partições (concessões).

**Consumidores:** consumidores ou trabalhos, são os threads que realizam o processamento do feed de alterações iniciado por cada host. Cada host de processador pode ter vários consumidores. Cada consumidor lê o feed de alterações da partição à qual ele é atribuído e notifica o respectivo host sobre as alterações e as concessões expiradas.

Para compreender melhor como esses quatro elementos do Processador do feed de alterações funcionam em conjunto, vamos examinar um exemplo no diagrama a seguir. A coleção monitorada armazena documentos e usa a "cidade" como a chave de partição. Podemos ver que a partição azul contém documentos com o campo "cidade" de "A a E" e assim por diante. Há dois hosts, cada um com dois consumidores lendo das quatro partições em paralelo. As setas mostram os consumidores lendo de um ponto específico no feed de alterações. Na primeira partição, o azul mais escuro representa as alterações não lidas enquanto que o azul claro representa as alterações já lidas no feed de alterações. Os hosts de usam a coleção de concessão para armazenar um valor de "continuação" a fim de manter um registro da posição atual de leitura para cada consumidor. 

![Usando o host do processador do feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Trabalhando com a biblioteca do Processador do Feed de Alterações

Antes de instalar o pacote NuGet do Processador do Feed de Alterações, instale: 

* Microsoft.Azure.DocumentDB, versão 1.13.1 ou superior 
* Newtonsoft.Json, versão 9.0.1 ou superior

Em seguida, instale o [pacote Nuget Microsoft.Azure.DocumentDB.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) e inclua-o como uma referência.

Para implementar a biblioteca do Processador de Feed de Alterações, é necessário fazer o seguinte:

1. Implementar um objeto **DocumentFeedObserver**, que implementa **IChangeFeedObserver**.

2. Implementar um **DocumentFeedObserverFactory**, que implementa um **IChangeFeedObserverFactory**.

3. No método **CreateObserver** de **DocumentFeedObserverFacory**, crie uma instância do **ChangeFeedObserver** criado na etapa 1 e retorne-o.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. Criar uma instância de **DocumentObserverFactory**.

5. Criar uma instância de um **ChangeFeedEventHost**:

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. Registrar o **DocumentFeedObserverFactory** com o host.

O código para as etapas 4 a 6 é: 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

É isso. Depois destas etapas, os documentos começarão a ir para o método **DocumentFeedObserver ProcessChangesAsync**.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o Azure Cosmos DB com Azure Functions, consulte [Azure Cosmos DB: computação de banco de dados sem servidor usando o Azure Functions](serverless-computing-database.md).

Para obter mais informações sobre como usar a biblioteca do Processador de Feed de Alterações, use os seguintes recursos:

* [página de informações](sql-api-sdk-dotnet-changefeed.md) 
* [Pacote do Nuget](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Código de exemplo mostrando as etapas 1 a 6 acima](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Exemplos adicionais sobre o GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Para obter mais informações sobre como usar o feed de alterações via SDK, use os seguintes recursos:

* [Página de informações do SDK](sql-api-sdk-dotnet.md)
