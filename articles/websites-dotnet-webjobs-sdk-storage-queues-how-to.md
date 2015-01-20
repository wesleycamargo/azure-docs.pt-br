<properties linkid="" pageTitle="Como trabalhar com armazenamento de fila do Azure usando o SDK de Trabalhos Web" metaKeywords="armazenamento de fila do Azure, SDK de trabalhos da Web, .NET c#" description="Saiba como trabalhar com o armazenamento de fila do Azure no S|DK de Trabalhos Web. Crie e exclua filas; insira; inspecione; obtenha e exclua as mensagens da fila e muito mais." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Como trabalhar com armazenamento de fila do Azure usando o SDK de Trabalhos Web

Este guia mostra como escrever o código C# para cenários comuns usando o
serviço de armazenamento de fila do Azure e o SDK de trabalhos Web do Azure, versão 1.0.0.

O guia pressupõe que você já sabe [o que é o SDK de trabalhos Web](../websites-webjobs-sdk-storage-queues-how-to) e [como executar tarefas básicas](../websites-dotnet-webjobs-sdk-get-started/), tais como instalar o pacote NuGet do SDK de trabalhos da Web, criar uma conta de armazenamento do Azure e criar cadeias de conexão para o SDK de trabalhos Web que apontam para a sua conta de armazenamento.

A maioria dos trechos de código mostra somente funções, não o código que cria o objeto `JobHost` como neste exemplo:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Sumário

-   [Como disparar uma função quando uma mensagem da fila é recebida ](#trigger)
	- Mensagens da fila da cadeia
	- Mensagens da fila do objeto POCO
	- Funções assíncronas
	- Algoritmo de sondagem
	- Execução paralela
	- Obter fila ou metadados de mensagem da fila
	- Desligamento normal
-   [Como criar uma mensagem da fila durante o processamento de uma mensagem da fila](#createqueue)
	- Mensagens da fila da cadeia
	- Mensagens da fila do objeto POCO
	- Criar várias mensagens
	- Usar atributos da fila no corpo de uma função
-   [Como ler e gravar blobs ao processar uma mensagem da fila](#blobs)
	- Mensagens da fila da cadeia
	- Mensagens da fila do objeto POCO
	- Usar atributos do Blob no corpo de uma função
-   [Como tratar mensagens suspeitas](#poison)
	- Manipulação automática de mensagens suspeitas
	- Manipulação manual de mensagens suspeitas
-   [Como definir as opções de configuração](#config)
	- Defina as cadeias de conexão do SDK no código
	- Definir configurações do QueueTrigger
	- Obter nomes de fila a partir da configuração
-   [Como disparar uma função manualmente](#manual)
-   [Como gravar logs](#logs)
-   [Próximas etapas](#nextsteps)

## <a id="trigger"></a> Como disparar uma função quando uma mensagem da fila é recebida

Para gravar uma função para que o SDK chame quando uma mensagem da fila for recebido, use o atributo `QueueTrigger` com um parâmetro cadeia ou de POCO, dependendo do que você espera para obter na mensagem. O construtor de atributo tem um parâmetro de cadeia que especifica o nome da fila para sondagem. Você também pode [definir dinamicamente o nome da fila](#config).

Se o site for executado em várias VMs, o trabalho Web será executado em todos os computadores e cada computador aguardará os gatilhos e tentará executar as funções. Em alguns cenários, isso pode fazer com que algumas funções processem os mesmos dados duas vezes. Assim, as funções devem ser idempotentes (escritas de forma que chamá-las repetidamente com os mesmos dados de entrada não produza resultados duplicados).  

### Mensagens da fila da cadeia

No exemplo a seguir, a fila contém uma mensagem de cadeia, portanto, o `QueueTrigger` é aplicado a um parâmetro de cadeia chamado `logMessage`, o qual contém o conteúdo da mensagem da fila. A função [grava uma mensagem de log no painel](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Além de `string`, o parâmetro pode ser uma matriz de bytes, um objeto `CloudQueueMessage` ou um objeto POCO que você define.

### Mensagens da fila do objeto POCO

No exemplo a seguir, a mensagem da fila contém JSON para um objeto `BlobInformation` que inclui uma propriedade `BlobName`. O SDK automaticamente desserializa o objeto.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

O exemplo a seguir mostra como criar uma mensagem da fila POCO sem usar o SDK de Trabalhos Web, a qual o SDK pode analisar. O SDK usa o [pacote NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar as mensagens.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Funções assíncronas

A seguinte função assíncrona [grava um log no painel](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

As funções assíncronas podem levar um token de cancelamento, conforme mostrado no exemplo a seguir, que copia um blob. (Para obter uma explicação sobre o espaço reservado `queueTrigger`, consulte a seção [Blobs](#blobs) .

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### Algoritmo de sondagem

O SDK implementa um algoritmo exponencial aleatório de retirada para reduzir o efeito de sondagem de fila ociosa nos custos das transações de armazenamento.  Quando uma mensagem for encontrada, o SDK aguarda dois segundos e, em seguida, verifica outra mensagem; quando nenhuma mensagem for encontrada, ele aguarda cerca de quatro segundos antes de tentar novamente. Após subsequentes tentativas falhas para obter uma mensagem da fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, cujo padrão é um minuto. [O tempo de espera máximo é configurável](#config).

### Execução paralela

Se você tiver várias funções escutando em filas diferentes, o SDK as chamará em paralelo quando as mensagens forem recebidas simultaneamente. 

O mesmo é verdadeiro quando várias mensagens são recebidas para uma única fila. Por padrão, o SDK obtém um lote de 16 mensagens de fila por vez e executa a função que as processa em paralelo. [O tamanho do lote é configurável](#config). Quando o número que está sendo processado chega até a metade do tamanho do lote, o SDK obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função é uma vez e meia o tamanho do lote. Esse limite se aplica separadamente a cada função que tem um atributo `QueueTrigger`. Se você não quiser uma execução paralela para mensagens recebidas em uma fila, defina o tamanho do lote como 1.

### <a id="queuemetadata"></a>Obter fila ou metadados de mensagem da fila

Você pode obter as propriedades da mensagem a seguir, adicionando parâmetros para a assinatura do método:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (contém o texto da mensagem)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Se você quiser trabalhar diretamente com a API de armazenamento do Azure, você também pode adicionar um parâmetro `CloudStorageAccount`.

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

Uma função que é executada em um trabalho Web contínuo pode aceitar um parâmetro `CancellationToken` que permite ao sistema operacional notificar a função quando o trabalho Web está prestes a ser encerrado. Você pode usar essa notificação para certificar-se de que a função não finalize inesperadamente de uma maneira que os dados fiquem em um estado inconsistente.

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

Para gravar uma função que crie uma nova mensagem da fila, use o atributo `Queue` em uma cadeia de saída ou parâmetro POCO. Como o `QueueTrigger`, você passa no nome da fila como uma cadeia ou você pode [definir dinamicamente o nome da fila](#config).

### Mensagens da fila da cadeia

O exemplo a seguir cria uma nova mensagem de fila na fila denominada "outputqueue" com o mesmo conteúdo que a mensagem da fila recebida na fila denominada "inputqueue".

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

Se o tipo de parâmetro de saída for qualquer um dos seguintes tipos e o objeto não for nulo quando a função tiver encerrado, o SDK criará uma mensagem da fila:

* `string` 
* `byte[]`
* Um tipo POCO serializável que você define
* `CloudQueueMessage`

Para criar várias mensagens, consulte **criar várias mensagens** mais adiante nesta seção.

Você também pode usar `CloudQueue` como o tipo de parâmetro de saída, se você quiser enviar mensagens manualmente.

### Mensagens da fila do objeto POCO

Para criar uma mensagem da fila que contenha um objeto POCO em vez de uma cadeia, passe o tipo POCO como um parâmetro de saída para o construtor de atributo `Queue`. 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

O SDK automaticamente serializa o objeto em JSON. Uma mensagem da fila sempre é criada, mesmo que o objeto seja nulo.

### Criar várias mensagens

Para criar várias mensagens, crie o tipo de parâmetro para a fila de saída `ICollector<T>` ou `IAsyncCollector`, conforme mostrado no exemplo a seguir.

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

### <a id="queuebody"></a>Usar atributos da fila no corpo de uma função

Se você precisar fazer algum trabalho na sua função antes de usar o atributo `Queue` para criar uma mensagem da fila, você pode usar a interface `IBinder` para obter um objeto `CloudQueue` que permite que você trabalhe diretamente com uma fila. 

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

## <a id="blobs"></a> Como ler e gravar blobs e tabelas durante o processamento de uma mensagem da fila

Os atributos `Blob` e `Table` permitem que você leia e grave os blobs e tabelas. Os exemplos nesta seção se aplicam a blobs.

Alguns dos tipos em que que o atributo `Blob` pode ser usado incluem `Stream`, `TextReader`, `TextWriter`, e `CloudBlockBlob`. O construtor de atributo utiliza um parâmetro `blobPath` que especifica o contêiner e o nome de blob, e quando o atributo decora um objeto `Stream`, o outro parâmetro de construtor especifica o modo `FileAccess` como leitura, gravação ou leitura/gravação. Se você precisar fazer algum trabalho em sua função antes de vincular um blob a um objeto, você pode usar o atributo no corpo da função, [conforme mostrado anteriormente para o atributo da fila](#queuebody).

### Mensagens da fila da cadeia

Para uma mensagem da fila que contenha uma cadeia de caracteres,`queueTrigger` é um espaço reservado que você pode usar no parâmetro `blobPath` do atributo `Blob` que contém o conteúdo da mensagem. 

O exemplo a seguir usa objetos `Stream` para ler e gravar os blobs. A mensagem da fila fornece o nome de um blob localizado no contêiner de textblobs. Uma cópia de blob com "-new" acrescentado ao nome é criada no mesmo contêiner. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

O exemplo a seguir usa um objeto `CloudBlockBlob` para excluir um blob.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### Mensagens da fila do objeto POCO

Para objetos POCO armazenados como JSON na mensagem da fila, você pode usar espaços reservados que nomeiam as propriedades do objeto no parâmetro `blobPath` do atributo `Queue`. Você também pode usar os [nomes de propriedade dos metadados da fila](#queuemetadata) como espaços reservados. 

O exemplo a seguir copia um blob em um novo blob com uma extensão diferente, usando as propriedades do objeto `BlobInformation` para especificar os nomes dos blobs de entrada e saída. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

O exemplo a seguir mostra como criar uma mensagem da fila POCO sem usar o SDK de Trabalhos Web, a qual o SDK pode analisar. O SDK usa o [pacote NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar as mensagens.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> Como tratar mensagens suspeitas

As mensagens cujo conteúdo faz com que uma função falhe são chamadas de *mensagens suspeitas*. Quando a função falhar, a mensagem da fila não é excluída e eventualmente é captada novamente, fazendo com que o ciclo seja repetido. O SDK pode interromper automaticamente o ciclo após algumas iterações, ou você fazê-lo manualmente.

### Manipulação automática de mensagens suspeitas

O SDK chamará uma função até 5 vezes para processar uma mensagem da fila. Se a quinta tentativa falhar, a mensagem é movida para uma fila de mensagens suspeitas. [O número máximo de tentativas é configurável](#config). 

A fila de mensagens suspeita é denominada *{originalqueuename}*-poison. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas ao registrá-las ou ao enviar uma notificação de que a atenção manual é necessária. 

No exemplo a seguir, a função `CopyBlob` falhará quando uma mensagem da fila conter o nome de um blob que não existe. Quando isso acontece, a mensagem será movida da fila copyblobqueue para a fila copyblobqueue-poison. O `ProcessPoisonMessage`, em seguida, registra a mensagem suspeita.

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

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Manipulação manual de mensagens suspeitas

Você pode obter o número de vezes que uma mensagem foi selecionada para processamento adicionando um parâmetro `int` chamado `dequeueCount` à sua função. Você pode, então, verificar a contagem de remoção da fila no código de função e realizar seu próprio tratamento de mensagem suspeita quando o número exceder um limite, conforme mostrado no exemplo a seguir.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 5)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

Para que este código funcione conforme o esperado, você precisa aumentar o número máximo de tentativas para o tratamento automático de mensagens suspeita ou a contagem de remoção da fila, neste exemplo nunca excederá 5.

## <a id="config"></a> Como definir as opções de configuração

Você pode usar o tipo `JobHostConfiguration` para definir as seguintes opções de configuração:

* Defina as cadeias de conexão do SDK no código.
* Defina as configurações de `QueueTrigger` com a máxima contagem de remoção da fila.
* Obtenha nomes de fila a partir da configuração.

### <a id="setconnstr"></a>Defina as cadeias de conexão do SDK no código

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

### <a id="configqueue"></a>Definir configurações de fila

Você pode configurar as seguintes configurações que se aplicam ao processamento de mensagem de fila:

- O número máximo de mensagens de fila que são recebidas simultaneamente para serem executadas em paralelo (o padrão é 16).
- O número máximo de tentativas antes de uma mensagem da fila ser enviada para uma fila de mensagens suspeitas (o padrão é 5).
- O tempo máximo de espera antes da sondagem ocorrer novamente quando uma fila está vazia (o padrão é 1 minuto).

O exemplo a seguir mostra como definir essas configurações:

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>Obter nomes de fila a partir da configuração

O tipo `JobHostConfiguration` lhe permite passar em um objeto `NameResolver` que fornece o nome da fila ao SDK para os atributos `QueueTrigger` e `Queue`.

Por exemplo, suponha que você deseje usar uma fila denominada logqueuetest no ambiente de teste e uma denominada logqueueprod na produção. Em vez de um nome de fila embutido em código, você deseja especificar o nome de uma entrada na coleção `appSettings` que teria o nome da fila real. Se a chave `appSettings` for logqueue, sua função pareceria com o exemplo a seguir.

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

Você passa a classe `NameResolver` no objeto `JobHost`, conforme mostrado no exemplo a seguir.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>Como disparar uma função manualmente

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

Para gravar logs que aparecem na página do painel dos trabalhos Web vinculada a uma invocação de função específica, use um objeto `TextWriter` obtido de um parâmetro na sua assinatura de método.

Para gravar [logs de rastreamento do aplicativo](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview), use `Console.Out` (cria logs marcados como INFO) e `Console.Error` (cria registros marcados como ERROR). Uma alternativa é usar [Trace ou TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx).

Os logs do aplicativo aparecem em arquivos de log do site, tabelas do Azure ou blobs do Azure, dependendo de como você configura seu site do Azure. Os 100 mais recentes logs do aplicativo também aparecem no painel se o programa estiver em execução em um trabalho Web do Azure. (Nenhum log de aplicativo será exibido no painel de um programa que estiver sendo executado localmente ou em algum outro ambiente.)   

Você pode desabilitar registro em log [definindo a cadeia de conexão do painel para nulo](#config).

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

No painel do SDK de trabalhos Web, a saída do objeto `TextWriter` aparece quando você vai até a página para uma invocação de função específica e clica em **Ativar saída**:

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

No painel do SDK de trabalhos Web, as últimas 100 linhas dos logs de aplicativo são mostradas quando você vai até a página para o trabalho Web (não para a invocação de função) e clica em **Alternar saída**.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

Em um trabalho Web contínuo, logs de aplicativo são mostrados em /data/jobs/continuous/*{webjobname}*/job_log.txt no sistema de arquivos do site.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Em um blob do Azure, a aparência dos logs de aplicativo é similar a esta:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

E, em uma tabela do Azure, os logs `Console.Out` e `Console.Error` têm esta aparência:

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Próximas etapas

Este tópico forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com filas do Azure. Para obter mais informações sobre como usar os Trabalhos Web do Azure e o SDK de Trabalhos Web, consulte [Trabalhos Web do Azure - Recursos recomendados](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=35.2-->
