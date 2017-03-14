---
title: Como usar o armazenamento de fila do Azure com o SDK de Trabalhos Web
description: Saiba como usar o armazenamento de fila do Azure com o SDK de Trabalhos Web. Criar e excluir filas; Inserir, inspecionar, obter e excluir a fila de mensagens e muito mais.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: dbfac5d9-f4a0-4e3e-9ecc-af3d7bf80463
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: fcbd9e10e4cc336dc6ea37f84201249e14b1af91
ms.openlocfilehash: 5110a86c3cc25ada27ddba9b0caef68e4509aa73
ms.lasthandoff: 12/08/2016


---
# <a name="how-to-use-azure-queue-storage-with-the-webjobs-sdk"></a>Como usar o armazenamento de fila do Azure com o SDK de Trabalhos Web
## <a name="overview"></a>Visão geral
Este guia fornece exemplos de código em C# que mostram como usar o SDK de Trabalhos Web do Azure versão 1.x com o serviço de armazenamento de fila do Azure.

Esse guia pressupõe que você sabe [como criar um projeto do Trabalho Web no Visual Studio com cadeias de conexão que apontam para sua conta de armazenamento](websites-dotnet-webjobs-sdk-get-started.md) ou para [várias contas de armazenamento](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs).

A maioria dos trechos de código mostra somente funções, não o código que cria o `JobHost` objeto como neste exemplo:

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

O guia inclui os seguintes tópicos:

* [Como acionar uma função quando uma mensagem da fila é recebida](#trigger)
  * Mensagens da fila da cadeia
  * Mensagem de fila POCO
  * Funções assíncronas
  * Tipos com os quais o atributo QueueTrigger funciona
  * Algoritmo de sondagem
  * Várias instâncias
  * Execução paralela
  * Obter fila ou metadados de mensagem da fila
  * Desligamento normal
* [Como criar uma mensagem da fila durante o processamento de uma mensagem da fila](#createqueue)
  * Mensagens da fila da cadeia
  * Mensagem de fila POCO
  * Criar várias mensagens ou em funções assíncronas
  * Tipos com os quais o atributo Queue funciona
  * Usar atributos do SDK de Trabalhos Web no corpo de uma função
* [Como ler e gravar blobs ao processar uma mensagem da fila](#blobs)
  * Mensagens da fila da cadeia
  * Mensagem de fila POCO
  * Tipos com os quais o atributo Blob funciona
* [Como tratar mensagens suspeitas](#poison)
  * Manipulação automática de mensagens suspeitas
  * Manipulação manual de mensagens suspeitas
* [Como definir as opções de configuração](#config)
  * Definir cadeias de conexão do SDK no código
  * Definir configurações de QueueTrigger
  * Definir valores para parâmetros do construtor do SDK WebJobs no código
* [Como acionar uma função manualmente](#manual)
* [Como gravar logs](#logs)
* [Como tratar erros e configurar tempos limite](#errors)
* [Próximas etapas](#nextsteps)

## <a id="trigger"></a> Como acionar uma função quando uma mensagem da fila é recebida
Para gravar uma função que o SDK de Trabalhos Web chama quando uma mensagem da fila é recebida, use o atributo `QueueTrigger` . O construtor de atributo tem um parâmetro de cadeia que especifica o nome da fila para sondagem. Você também pode [definir dinamicamente o nome da fila](#config).

### <a name="string-queue-messages"></a>Mensagens da fila da cadeia
No exemplo a seguir, a fila contém uma mensagem de cadeia. Portanto, `QueueTrigger` é aplicado a um parâmetro de cadeia chamado `logMessage`, o qual contém o conteúdo da mensagem da fila. A função [grava uma mensagem de log no painel](#logs).

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

Além de `string`, o parâmetro pode ser uma matriz de bytes, um objeto `CloudQueueMessage` ou um POCO que você define.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Mensagens de fila POCO [(Objeto Plain Old CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
No exemplo a seguir, a mensagem da fila contém JSON para um objeto `BlobInformation` que inclui uma propriedade `BlobName`. O SDK automaticamente desserializa o objeto.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

O SDK usa o [pacote NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar as mensagens. Se criar mensagens de fila em um programa que não usa o SDK de Trabalhos Web, você poderá escrever código como o exemplo a seguir para criar uma mensagem de fila POCO que o SDK possa analisar.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Funções assíncronas
A seguinte função assíncrona [grava um log no painel](#logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

As funções assíncronas podem levar um [token de cancelamento](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), conforme mostrado no exemplo a seguir, que copia um blob. (Para obter uma explicação sobre o espaço reservado `queueTrigger` , consulte a seção [Blobs](#blobs) .)

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

### <a id="qtattributetypes"></a> Tipos com os quais o atributo QueueTrigger funciona
Você pode usar `QueueTrigger` com os seguintes tipos:

* `string`
* Um tipo POCO serializado como JSON
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a> Algoritmo de sondagem
O SDK implementa um algoritmo exponencial aleatório de retirada para reduzir o efeito de sondagem de fila ociosa nos custos das transações de armazenamento.  Quando uma mensagem for encontrada, o SDK aguarda dois segundos e, em seguida, verifica outra mensagem; quando nenhuma mensagem for encontrada, ele aguarda cerca de quatro segundos antes de tentar novamente. Após subsequentes tentativas falhas para obter uma mensagem da fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, cujo padrão é um minuto. [O tempo de espera máximo é configurável](#config).

### <a id="instances"></a> Várias instâncias
Se seu aplicativo Web for executado em várias instâncias, um Trabalho Web contínuo será executado em todos os computadores, e cada computador aguardará os gatilhos e tentará executar as funções. O gatilho de fila do SDK dos Trabalhos Web impede automaticamente que uma função processe uma mensagem da fila várias vezes; as funções não precisam ser escritas para ser idempotentes. No entanto, se desejar garantir que apenas uma instância de uma função é executada, mesmo quando houver várias instâncias do aplicativo Web host, é possível usar o atributo `Singleton` .

### <a id="parallel"></a> Execução paralela
Se você tiver várias funções escutando em filas diferentes, o SDK as chamará em paralelo quando as mensagens forem recebidas simultaneamente.

O mesmo é verdadeiro quando várias mensagens são recebidas para uma única fila. Por padrão, o SDK obtém um lote de 16 mensagens de fila por vez e executa a função que as processa em paralelo. [O tamanho do lote é configurável](#config). Quando o número que está sendo processado chega até a metade do tamanho do lote, o SDK obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função é uma vez e meia o tamanho do lote. Esse limite se aplica separadamente a cada função que tem um atributo `QueueTrigger` .

Se não desejar uma execução paralela para mensagens recebidas em uma fila, é possível definir o tamanho do lote como 1. Veja também **Mais controle sobre o processamento de fila** no [RTM 1.1.0 do SDK dos Trabalhos Web do Azure](https://azure.microsoft.com/blog/azure-webjobs-sdk-1-1-0-rtm/).

### <a id="queuemetadata"></a>Obter fila ou metadados de mensagem da fila
Você pode obter as propriedades da mensagem a seguir adicionando parâmetros à assinatura do método:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contém o texto da mensagem)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Se desejar trabalhar diretamente com a API de armazenamento do Azure, também é possível adicionar um parâmetro `CloudStorageAccount` .

O exemplo a seguir grava todos os metadados em um log de aplicativo de informações. No exemplo, tanto logMessage quanto queueTrigger contém o conteúdo da mensagem da fila.

        public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
            DateTimeOffset expirationTime,
            DateTimeOffset insertionTime,
            DateTimeOffset nextVisibleTime,
            string id,
            string popReceipt,
            int dequeueCount,
            string queueTrigger,
            CloudStorageAccount cloudStorageAccount,
            TextWriter logger)
        {
            logger.WriteLine(
                "logMessage={0}\n" +
            "expirationTime={1}\ninsertionTime={2}\n" +
                "nextVisibleTime={3}\n" +
                "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
                "queue endpoint={7} queueTrigger={8}",
                logMessage, expirationTime,
                insertionTime,
                nextVisibleTime, id,
                popReceipt, dequeueCount,
                cloudStorageAccount.QueueEndpoint,
                queueTrigger);
        }

Este é um log de exemplo gravado pelo código de exemplo:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

### <a id="graceful"></a>Desligamento normal
Uma função que é executada em um trabalho Web contínuo pode aceitar um parâmetro `CancellationToken` , que permite ao sistema operacional notificar a função quando o trabalho Web está prestes a ser encerrado. Você pode usar essa notificação para certificar-se de que a função não finalize inesperadamente de uma maneira que os dados fiquem em um estado inconsistente.

O exemplo a seguir mostra como verificar se há eminência de encerramento do trabalho Web em uma função.

    public static void GracefulShutdownDemo(
                [QueueTrigger("inputqueue")] string inputText,
                TextWriter logger,
                CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(1000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }

**Observação:** o painel pode não mostrar corretamente o status e a saída das funções que foram desligadas.

Para obter mais informações, consulte [Desligamento normal dos trabalhos Web](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a> Como criar uma mensagem da fila durante o processamento de uma mensagem da fila
Para gravar uma função que cria uma nova mensagem da fila, use o atributo `Queue` . Como o `QueueTrigger`, você passa o nome da fila como uma cadeia de caracteres ou pode [definir o nome da fila dinamicamente](#config).

### <a name="string-queue-messages"></a>Mensagens da fila da cadeia
O exemplo de código não síncrono a seguir cria uma nova mensagem de fila na fila denominada "outputqueue" com o mesmo conteúdo que a mensagem da fila recebida na fila denominada "inputqueue". (Para funções assíncronas, use `IAsyncCollector<T>` , conforme mostrado posteriormente nesta seção.)

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Mensagens de fila POCO [(Objeto Plain Old CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Para criar uma mensagem da fila que contenha um POCO em vez de uma cadeia de caracteres, passe o tipo POCO como um parâmetro de saída para o construtor de atributo `Queue` .

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

O SDK serializa automaticamente o objeto em JSON. Uma mensagem da fila sempre é criada, mesmo que o objeto seja nulo.

### <a name="create-multiple-messages-or-in-async-functions"></a>Criar várias mensagens ou em funções assíncronas
Para criar várias mensagens, crie o tipo de parâmetro para a fila de saída `ICollector<T>` ou `IAsyncCollector<T>`, conforme mostrado no exemplo a seguir.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Cada mensagem da fila é criada imediatamente quando o método `Add` é chamado.

### <a name="types-that-the-queue-attribute-works-with"></a>Tipos com os quais o atributo Queue funciona
Você pode usar o atributo `Queue` nos seguintes tipos de parâmetros:

* `out string` (criará a mensagem da fila se o valor do parâmetro não for nulo quando a função terminar)
* `out byte[]` (funciona como `string`)
* `out CloudQueueMessage` (funciona como `string`)
* `out POCO` (um tipo serializável criará uma mensagem com um objeto nulo se o parâmetro for nulo quando a função terminar)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (para criar mensagens manualmente usando a API de Armazenamento do Azure diretamente)

### <a id="ibinder"></a>Usar atributos do SDK de Trabalhos Web no corpo de uma função
Se precisar realizar algum trabalho em sua função antes de usar um atributo do SDK de Trabalhos Web como `Queue`, `Blob`, ou `Table`, você poderá usar a interface `IBinder`.

O exemplo a seguir usa uma mensagem da fila de entrada e cria uma nova mensagem com o mesmo conteúdo em uma fila de saída. O nome da fila de saída é definido pelo código no corpo da função.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

A interface `IBinder` também pode ser usada com os atributos `Table` e `Blob`.

## <a id="blobs"></a> Como ler e gravar blobs e tabelas durante o processamento de uma mensagem da fila
Os atributos `Blob` e `Table` permitem que você leia e grave os blobs e tabelas. Os exemplos nesta seção se aplicam a blobs. Para obter exemplos de código que mostram como disparar processos quando blobs são criados ou atualizados, consulte [Como usar o armazenamento de blobs do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) e para obter exemplos de código que leem e gravam as tabelas, consulte [Como usar o armazenamento de tabelas do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### <a name="string-queue-messages-triggering-blob-operations"></a>Mensagens da fila de cadeia de caracteres que disparam operações de blob
Para uma mensagem da fila que contém uma cadeia de caracteres, `queueTrigger` é um espaço reservado que você pode usar no parâmetro do `Blob` atributo `blobPath` que tem o conteúdo da mensagem.

O exemplo a seguir usa objetos `Stream` para ler e gravar os blobs. A mensagem da fila é o nome de um blob localizado no contêiner de textblobs. Uma cópia de blob com "-new" acrescentado ao nome é criada no mesmo contêiner.

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

O atributo construtor `Blob` aceita um parâmetro `blobPath` que especifica o nome do blob e contêiner. Para obter mais informações sobre esse espaço reservado, consulte [Como usar o armazenamento de blob do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md),

Quando o atributo decora um objeto `Stream`, outro parâmetro de construtor especifica o modo `FileAccess` como leitura, gravação ou leitura/gravação.

O exemplo a seguir usa um objeto `CloudBlockBlob` para excluir um blob. A mensagem da fila é o nome do blob.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a id="pocoblobs"></a> Mensagens de fila POCO [(Objeto Plain Old CLR](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Para um POCO armazenado como JSON na mensagem da fila, você pode usar espaços reservados que nomeiam as propriedades do objeto no parâmetro do `Queue` atributo `blobPath`. Também é possível utilizar os [nomes de propriedade de metadados de fila](#queuemetadata) como espaços reservados.

O exemplo a seguir copia um blob para um novo blob com uma extensão diferente. A mensagem da fila é um objeto `BlobInformation` que inclui `BlobName` e as propriedades `BlobNameWithoutExtension`. Os nomes de propriedade são usados como espaços reservados no caminho de blob para os atributos `Blob` .

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

O SDK usa o [pacote NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar as mensagens. Se criar mensagens de fila em um programa que não usa o SDK de Trabalhos Web, você poderá escrever código como o exemplo a seguir para criar uma mensagem de fila POCO que o SDK possa analisar.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Se você precisar fazer algum trabalho em sua função antes de vincular um blob a um objeto, você pode usar o atributo no corpo da função, [conforme mostrado anteriormente para o atributo da fila](#ibinder).

### <a id="blobattributetypes"></a> Tipos com os quais você pode usar o atributo Blob
O atributo `Blob` pode ser usado com os seguintes tipos:

* `Stream` (leitura ou gravação, especificadas usando o parâmetro de construtor FileAccess)
* `TextReader`
* `TextWriter`
* `string` (leitura)
* `out string` (gravação; criará um blob somente se o parâmetro de cadeia de caracteres não for nulo quando a função retornar)
* POCO (leitura)
* out POCO (gravação; sempre cria um blob; criará como objeto nulo se o parâmetro POCO for nulo quando a função retornar)
* `CloudBlobStream` (gravação)
* `ICloudBlob` (leitura ou gravação)
* `CloudBlockBlob` (leitura ou gravação)
* `CloudPageBlob` (leitura ou gravação)

## <a id="poison"></a> Como tratar mensagens suspeitas
As mensagens cujo conteúdo faz com que uma função falhe são chamadas de *mensagens suspeitas*. Quando a função falhar, a mensagem da fila não é excluída e eventualmente é captada novamente, fazendo com que o ciclo seja repetido. O SDK pode interromper automaticamente o ciclo após algumas iterações, ou você fazê-lo manualmente.

### <a name="automatic-poison-message-handling"></a>Manipulação automática de mensagens suspeitas
O SDK chamará uma função até 5 vezes para processar uma mensagem da fila. Se a quinta tentativa falhar, a mensagem é movida para uma fila de mensagens suspeitas. [O número máximo de novas tentativas é configurável](#config).

A fila de mensagens suspeita é denominada *{originalqueuename}*-suspeita. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária.

No exemplo a seguir, a função `CopyBlob` falhará quando uma mensagem da fila contiver o nome de um blob que não existe. Quando isso acontece, a mensagem será movida da fila copyblobqueue para a fila copyblobqueue-poison. O `ProcessPoisonMessage` registra então a mensagem suspeita.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

        public static void ProcessPoisonMessage(
            [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
        {
            logger.WriteLine("Failed to copy blob, name=" + blobName);
        }

A ilustração a seguir mostra a saída do console dessas funções quando uma mensagem suspeita é processada.

![Saída do console para tratamento de mensagens suspeitas](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### <a name="manual-poison-message-handling"></a>Manipulação manual de mensagens suspeitas
Você pode obter o número de vezes que uma mensagem foi selecionada para processamento adicionando um parâmetro `int` chamado `dequeueCount` à sua função. Você pode, então, verificar a contagem de remoção da fila no código de função e realizar seu próprio tratamento de mensagem suspeita quando o número exceder um limite, conforme mostrado no exemplo a seguir.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## <a id="config"></a> Como definir as opções de configuração
Você pode usar o tipo `JobHostConfiguration` para definir as seguintes opções de configuração:

* Definir as cadeias de conexão do SDK no código.
* Defina as configurações `QueueTrigger` de como a contagem máxima de remoção da fila.
* Obtenha nomes de fila por meio da configuração.

### <a id="setconnstr"></a>Definir as cadeias de conexão do SDK no código
A definição das cadeias de conexão do SDK no código lhe permite usar seus próprios nomes de cadeia de conexão em arquivos de configuração ou variáveis de ambiente, conforme mostrado no exemplo a seguir.

        static void Main(string[] args)
        {
            var _storageConn = ConfigurationManager
                .ConnectionStrings["MyStorageConnection"].ConnectionString;

            var _dashboardConn = ConfigurationManager
                .ConnectionStrings["MyDashboardConnection"].ConnectionString;

            var _serviceBusConn = ConfigurationManager
                .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

            JobHostConfiguration config = new JobHostConfiguration();
            config.StorageConnectionString = _storageConn;
            config.DashboardConnectionString = _dashboardConn;
            config.ServiceBusConnectionString = _serviceBusConn;
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="configqueue"></a>Configurar definições de QueueTrigger
Você pode definir as seguintes configurações que se aplicam ao processamento de mensagem de fila:

* O número máximo de mensagens da fila que são recebidas simultaneamente a serem executadas em paralelo (o padrão é 16).
* O número máximo de tentativas antes de uma mensagem da fila ser enviada para uma fila de suspeita (o padrão é 5).
* O tempo máximo de espera antes de sondar novamente quando uma fila está vazia (o padrão é 1 minuto).

O exemplo a seguir mostra como definir essas configurações:

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="setnamesincode"></a>Definir valores para parâmetros do construtor do SDK WebJobs no código
Às vezes você deseja especificar um nome de fila, um nome de blob ou contêiner ou um nome de tabela no código em vez de embuti-lo no código. Por exemplo, você talvez queira especificar o nome da fila para `QueueTrigger` em um arquivo de configuração ou variável de ambiente.

Você pode fazer isso passando um objeto `NameResolver` para o tipo `JobHostConfiguration`. Você inclui espaços reservados especiais entre sinais de percentual (%) em parâmetros do construtor de atributos do SDK de e o código `NameResolver` especifica os valores reais a serem usados no lugar desses espaços reservados.

Por exemplo, suponha que você deseje usar uma fila denominada logqueuetest no ambiente de teste e uma denominada logqueueprod na produção. Em vez de um nome de fila embutido em código, você deseja especificar o nome de uma entrada na coleção `appSettings` que teria o nome da fila real. Se a chave `appSettings` for logqueue, sua função será semelhante ao exemplo a seguir.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

Sua classe `NameResolver` poderia, então, obter o nome da fila de `appSettings`, conforme mostrado no exemplo a seguir:

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

Você passa a classe `NameResolver` ao objeto `JobHost`, conforme mostrado no exemplo a seguir.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**Observação:** os nomes de fila, tabela e blob são resolvidos sempre que uma função é chamada, mas os nomes de contêineres de blob são resolvidos somente quando o aplicativo é iniciado. Você não pode alterar o nome do contêiner de blob enquanto o trabalho está em execução.

## <a id="manual"></a>Como acionar uma função manualmente
Para disparar uma função manualmente, use o método `Call` ou `CallAsync` no objeto `JobHost` e o atributo `NoAutomaticTrigger` na função, conforme mostrado no exemplo a seguir.

        public class Program
        {
            static void Main(string[] args)
            {
                JobHost host = new JobHost();
                host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
            }

            [NoAutomaticTrigger]
            public static void CreateQueueMessage(
                TextWriter logger,
                string value,
                [Queue("outputqueue")] out string message)
            {
                message = value;
                logger.WriteLine("Creating queue message: ", message);
            }
        }

## <a id="logs"></a>Como gravar logs
O painel mostra logs em dois lugares: na página do Trabalho Web e na página de determinada invocação do Trabalho Web.

![Logs na página do Trabalho Web](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Logs na página de invocação de função](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

A saída de métodos de console que você chama em uma função ou no método `Main()` aparece na página Painel para o trabalho Web, não na página de uma invocação de método específica. A saída do objeto TextWriter obtido de um parâmetro na assinatura do método é exibida na página Painel para uma invocação de método.

A saída do console não pode ser vinculada a uma invocação de método específica porque o console é single-threaded, embora muitas funções de trabalho possam estar sendo executadas ao mesmo tempo. É por isso que o SDK fornece a cada invocação de função seu próprio objeto de gravador de log exclusivo.

Para gravar [logs de rastreamento do aplicativo](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), utilize `Console.Out` (cria registros marcados como INFO) e `Console.Error` (cria registros marcados como ERROR). Uma alternativa é usar [Rastreamento ou TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), que fornece os níveis Detalhado, de Aviso e Crítico, além de Informações e Erro. Os logs de rastreamento de aplicativo aparecem nos arquivos de log de aplicativo Web, nas tabelas do Azure ou nos blobs do Azure, dependendo de como você configura seu aplicativo web do Azure. Assim como ocorre com toda a saída do console, os 100 logs de aplicativos mais recentes também são exibidos na página Painel do Trabalho Web, não na página de uma invocação de função.

A saída do console aparecerá no painel somente se o programa estiver em execução em um Trabalho Web do Azure, não se o programa estiver em execução localmente ou em outro ambiente.

Desabilite o log de painel para cenários de alta taxa de transferência. Por padrão, o SDK grava logs no armazenamento e essa atividade pode degradar o desempenho durante o processamento de várias mensagens. Para desabilitar o log, defina a cadeia de conexão do painel de controle como nula, como mostrado no exemplo a seguir.

        JobHostConfiguration config = new JobHostConfiguration();       
        config.DashboardConnectionString = "";        
        JobHost host = new JobHost(config);
        host.RunAndBlock();

O exemplo a seguir mostra várias maneiras de gravar logs:

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

No painel do SDK de Trabalhos Web, a saída do objeto `TextWriter` aparece quando você vai até a página para uma invocação de função específica e clica em **Alternar Saída**:

![Clique no link de invocação de função](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs na página de invocação de função](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

No painel do SDK de Trabalhos Web, as 100 linhas da saída do console mais recentes são mostradas quando você vai até a página do Trabalho Web (não da invocação de função) e clica em **Alternar Saída**.

![Clique em Alternar Saída](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

Em um Trabalho Web contínuo, os logs de aplicativo são mostrados em /data/jobs/continuous/*{nomedowebjob}*/job_log.txt no sistema de arquivos do aplicativo Web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Em um blob do Azure, os logs de aplicativo se parecem com este: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Olá, mundo!,

E em uma tabela do Azure, os logs `Console.Out` e `Console.Error` têm esta aparência:

![Log de informações na tabela](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Log de erros na tabela](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

Se você desejar conectar seu próprio agente, veja [este exemplo](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Program.cs).

## <a id="errors"></a>Como tratar erros e configurar tempos limite
O SDK dos Trabalhos Web também inclui um atributo [Timeout](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs) que pode ser usado para fazer com que uma função seja cancelada se não for concluída em um período especificado. E se você desejar gerar um alerta quando um número excessivo de erros ocorrerem em um período especificado, é possível usar o atributo `ErrorTrigger` . Este é um [exemplo de ErrorTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Error-Monitoring).

```
public static void ErrorMonitor(
[ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
[SendGrid(
    To = "admin@emailaddress.com",
    Subject = "Error!")]
 SendGridMessage message)
{
    // log last 5 detailed errors to the Dashboard
   log.WriteLine(filter.GetDetailedMessage(5));
   message.Text = filter.GetDetailedMessage(1);
}
```

Você também pode desabilitar e habilitar de modo dinâmico funções para controlar se eles podem ser disparados, usando uma opção de configuração que pode ser uma configuração de aplicativo ou um nome de variável de ambiente. Para obter o código de exemplo, veja o atributo `Disable` no [repositório de exemplos de SDK dos Trabalhos Web](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs).

## <a id="nextsteps"></a> Próximas etapas
Este guia forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com filas do Azure. Para obter mais informações sobre como usar os Trabalhos Web do Azure e o SDK de Trabalhos Web, consulte [Trabalhos Web do Azure – Recursos recomendados](http://go.microsoft.com/fwlink/?linkid=390226).

