---
title: Como usar o WebJobs SDK para o processamento em segundo plano controlado por evento - Azure
description: Saiba mais sobre como escrever código para o WebJobs SDK. Crie trabalhos de processamento em segundo plano controlado por evento que acessam dados nos serviços de terceiros e serviços do Azure.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 3adf725f76f744fd1d321668fe892b9703de25de
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2018
---
# <a name="how-to-use-the-webjobs-sdk-for-event-driven-background-processing"></a>Como usar o WebJobs SDK para o processamento em segundo plano controlado por evento

Este artigo fornece orientação sobre como escrever código para o [WebJobs SDK](webjobs-sdk-get-started.md). A documentação se aplica às versões 2.x e 3.x, exceto quando indicado em contrário. A principal mudança introduzida por 3.x é o uso do .NET Core em vez do .NET Framework.

>[!NOTE]
> O [Azure Functions](../azure-functions/functions-overview.md) é compilado no WebJobs SDK, e este artigo apresenta links para a documentação do Azure Functions para alguns tópicos. Observe as seguintes diferenças entre o Functions e o WebJobs SDK:
> * O Azure Functions versão 1. x corresponde ao WebJobs SDK versão 2.x, e o Azure Functions 2.x corresponde ao WebJobs SDK 3.x. Os repositórios de código-fonte seguem a numeração do WebJobs SDK, e muitos têm branches v2.x, com o branch mestre atualmente tendo o código de 3.x.
> * O código de exemplo para bibliotecas de classe C# do Azure Functions é como o código do WebJobs SDK, exceto que não é necessário um atributo `FunctionName` em um projeto do WebJobs SDK.
> * Alguns tipos de associação são suportados apenas no Functions, como HTTP, webhook e Grade de Eventos (que é baseado em HTTP). 
> 
> Para obter mais informações, consulte [Comparar o WebJobs SDK e o Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>pré-requisitos

Este artigo pressupõe que você leu [Introdução ao WebJobs SDK](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

O objeto `JobHost` é o contêiner de tempo de execução de funções: ele escuta gatilhos e chama funções. Você cria o `JobHost` no seu código e escreve um código para personalizar seu comportamento.

Essa é uma diferença importante entre usar diretamente o WebJobs SDK e usá-lo indiretamente utilizando o Azure Functions. No Azure Functions, o serviço controla o `JobHost`, e não é possível personalizá-lo escrevendo código. O Azure Functions permite que você personalize o comportamento de host por meio de configurações no arquivo *host.json*. Essas configurações são cadeias de caracteres, não código, que limitam os tipos de personalizações que você pode fazer.

### <a name="jobhost-connection-strings"></a>Cadeias de conexão de JobHost

O WebJobs SDK procura as cadeias de conexão de Armazenamento e Barramento de Serviço em *local.settings.json* quando você executa localmente ou no ambiente do WebJob quando você executa no Azure. Se você quiser usar seus próprios nomes para essas cadeias de conexão ou armazená-los em outro lugar, você poderá defini-los no código, como mostrado aqui:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>Configurações de desenvolvimento de JobHost

A classe `JobHostConfiguration` tem um método `UseDevelopmentSettings` que você pode chamar para tornar o desenvolvimento local mais eficiente. Aqui estão algumas das configurações que esse método altera:

| Propriedade | Configuração de desenvolvimento |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` para maximizar a saída de log. |
| `Queues.MaxPollingInterval`  | Um valor baixo para garantir que os métodos de fila sejam disparados imediatamente.  |
| `Singleton.ListenerLockPeriod` | Quinze segundos para ajudar no desenvolvimento iterativo rápido. |

O exemplo a seguir mostra como usar as configurações de desempenho. Para fazer `config.IsDevelopment` retornar `true` quando em execução localmente, defina uma variável de ambiente local denominada `AzureWebJobsEnv` com o valor `Development`.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>Configurações de JobHost ServicePointManager

O .NET Framework contém uma API chamada [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) que controla o número de conexões simultâneas para um host. É recomendável que você aumente esse valor do padrão de 2 antes de iniciar o host do WebJobs.

Todas as solicitações HTTP de saída feitas por meio de uma função usando o fluxo de `HttpClient` por meio de `ServicePointManager`. Quando você atingir `DefaultConnectionLimit`, `ServicePointManager` iniciará as solicitações de enfileiramento antes de enviá-las. Suponha que seu `DefaultConnectionLimit` seja definido como 2 e seu código faça 1.000 solicitações HTTP. Inicialmente, apenas 2 solicitações, na verdade, são permitidas por meio do sistema operacional. As outras 998 são colocadas na fila até que haja espaço para elas. Isso significa que `HttpClient` pode atingir o tempo limite, porque ele *considera* ele fez a solicitação, mas a solicitação nunca foi enviada pelo sistema operacional para o servidor de destino. Para que você possa ver o comportamento que não faz sentido: o `HttpClient` local está demorando 10 segundos para concluir uma solicitação, mas o serviço está retornando cada solicitação em 200 ms. 

O valor padrão para aplicativos ASP.NET é `Int32.MaxValue`, e é provável que funcione bem para WebJobs em execução em um plano Básico ou um plano do Serviço de Aplicativo superior. Os WebJobs normalmente precisam de configuração Always On, e isso é suportado apenas pelo planos Básico e do Serviço de Aplicativo superior. 

Caso o WebJob esteja em execução em um Plano do Serviço de Aplicativo Gratuito ou Compartilhado, seu aplicativo ficará restrito à área restrita do Serviço de Aplicativo, que atualmente tem um [limite de conexão de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Com um limite de conexão não associado no `ServicePointManager`, é mais provável que seja atingido o limite de conexão de área restrita, e o site seja desativado. Nesse caso, a configuração de `DefaultConnectionLimit` para algo inferior, como 50 ou 100, pode evitar que isso aconteça e ainda permitir uma taxa de transferência suficiente.

A configuração deve ser definida antes de todas as solicitações HTTP serem feitas. Por esse motivo, o host de WebJobs não deve tentar ajustar a configuração automaticamente. Pode haver solicitações HTTP que ocorrem antes de o host ser iniciado, e isso pode resultar em comportamento inesperado. A melhor abordagem é definir o valor imediatamente no método `Main` antes de inicializar o `JobHost`, conforme mostrado no exemplo a seguir

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Gatilhos

As funções devem ser métodos públicos e devem ter um atributo de gatilho no atributo [NoAutomaticTrigger](#manual-trigger).

### <a name="automatic-trigger"></a>Gatilho automático

Os disparadores automáticos chamam uma função em resposta a um evento. Para obter um exemplo, consulte o gatilho de fila no [artigo Introdução](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Gatilho manual

Para disparar uma função manualmente, use o atributo `NoAutomaticTrigger`, conforme mostrado no exemplo a seguir:

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>Associações de entrada e saída

As associações de entrada e saída fornecem uma maneira declarativa para criar dados a partir do Azure ou de serviços externos disponíveis para seu código. As associações de saída fornecem uma maneira de atualizar os dados. O [artigo Introdução](webjobs-sdk-get-started.md) mostra um exemplo de cada um.

Você pode usar um valor de retorno do método para uma associação de saída, aplicando o atributo ao valor de retorno do método. Consulte o exemplo no artigo [Gatilhos e associações](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) do Azure Functions.

## <a name="binding-types"></a>Tipos de associação

Os seguintes tipos de gatilho e associação estão incluídos no pacote `Microsoft.Azure.WebJobs`:

* Armazenamento de blob
* Armazenamento de filas
* Armazenamento de tabela

Para usar outros tipos de associação e gatilho, instale o pacote do NuGet que os contém e chame um método `Use<binding>` no objeto `JobHostConfiguration`. Por exemplo, se você quiser usar um gatilho de Temporizador, instalar `Microsoft.Azure.WebJobs.Extensions` e chamar `UseTimers` no método `Main`, como neste exemplo:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Você pode encontrar o pacote de instalação para um tipo específico de associação na seção **Pacotes** do [artigo de referência](#binding-reference-information) desse tipo de associação para o Azure Functions. Uma exceção é o gatilho e a associação de Arquivos (para o sistema de arquivos local), o que não é suportado pelo Azure Functions. Para usar a associação de Arquivos, instale `Microsoft.Azure.WebJobs.Extensions` e chame `UseFiles`.

### <a name="usecore"></a>UseCore

O pacote `Microsoft.Azure.WebJobs.Extensions` mencionado anteriormente também fornece um tipo especial de associação que você pode registrar chamando o método `UseCore`. Essa associação permite que você defina um parâmetro [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) em sua assinatura de função. O objeto de contexto dá acesso à ID de invocação, que pode ser usada para correlacionar todos os logs produzidos por uma determinada chamada de função. Aqui está um exemplo:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

## <a name="binding-configuration"></a>Configuração de associação

Vários tipos de gatilho e associação permitem que você configure o comportamento definindo propriedades em um objeto de configuração que você transmite para o `JobHost`.

### <a name="queue-trigger-configuration"></a>Configuração de gatilho de fila

As configurações que você pode definir para o gatilho de fila de Armazenamento são explicadas na [referência host.json](../azure-functions/functions-host-json.md#queues) do Azure Functions. Como defini-las em um projeto do WebJobs SDK é mostrado no exemplo a seguir:

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configuration-for-other-bindings"></a>Configuração de outras associações

Alguns tipos de gatilho e associação definem seu próprio tipo de configuração personalizado. Por exemplo, o gatilho de Arquivo permite especificar o caminho raiz para monitorar:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Expressões de associação

Nos parâmetros do construtor de atributo, você pode usar expressões que são resolvidas para valores de várias fontes. Por exemplo, no código a seguir, o caminho para o atributo `BlobTrigger` cria uma expressão nomeada `filename`. Quando usado para a associação de saída, `filename` é resolvido para o nome do blob de disparo.
 
```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Para obter mais informações sobre expressões de associação, consulte [Padrões e expressões de associação](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) na documentação do Azure Functions.

### <a name="custom-binding-expressions"></a>Expressões de associação personalizadas

Às vezes você deseja especificar um nome de fila, um nome de blob ou contêiner ou um nome de tabela no código em vez de embuti-lo no código. Por exemplo, você talvez queira especificar o nome da fila para o atributo `QueueTrigger` em um arquivo de configuração ou uma variável de ambiente.

Você pode fazer isso transferindo um objeto `NameResolver` para o objeto `JobHostConfiguration`. Você inclui espaços reservados no gatilho ou parâmetros do construtor de atributo de associação, e seu código `NameResolver` fornece os valores reais a serem usados no lugar desses espaços reservados. Os espaços reservados são identificados colocando-os entre sinais de porcentagem (%), conforme mostrado no exemplo a seguir:
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Este código lhe permite usar uma fila denominada logqueuetest no ambiente de teste e uma denominada logqueueprod na produção. Em vez de um nome de fila embutido em código, você especifica o nome de uma entrada na coleção `appSettings`. 

Não há um NameResolver padrão que entrará em vigor se você não fornecer um personalizado. O padrão obtém valores de configurações de aplicativo ou variáveis de ambiente.

A classe `NameResolver` obtém o nome da fila de `appSettings`, conforme mostrado no exemplo a seguir:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Transfira a classe `NameResolver` para o objeto `JobHost`, conforme mostrado no exemplo a seguir:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

O Azure Functions implementa `INameResolver` para obter valores de configurações do aplicativo, conforme mostrado no exemplo. Quando usa o WebJobs SDK diretamente, você pode escrever uma implementação personalizada que obtém valores de substituição de espaço reservado de qualquer fonte preferida. 

## <a name="binding-at-runtime"></a>Associando no tempo de execução

Se precisar realizar algum trabalho em sua função antes de usar um atributo de associação, como `Queue`, `Blob`, ou `Table`, você poderá usar a interface `IBinder`.

O exemplo a seguir usa uma mensagem da fila de entrada e cria uma nova mensagem com o mesmo conteúdo em uma fila de saída. O nome da fila de saída é definido pelo código no corpo da função.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Para obter mais informações, consulte [Associação no tempo de execução](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) na documentação do Azure Functions.

## <a name="binding-reference-information"></a>Informações de referência de associação

Informações de referência sobre cada tipo de associação são fornecidas na documentação do Azure Functions. Usando a fila de Armazenamento como um exemplo, você encontrará as informações a seguir em cada artigo de referência de associação:

* [Pacotes](../azure-functions/functions-bindings-storage-queue.md#packages) -Qual pacote instalar para incluir suporte para a associação em um projeto do WebJobs SDK.
* [Exemplos](../azure-functions/functions-bindings-storage-queue.md#trigger---example) - O exemplo da biblioteca de classes C# se aplica ao WebJobs SDK; apenas omita o atributo `FunctionName`.
* [Atributos](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) -Os atributos a serem usados para o tipo de associação.
* [Configuração](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) -Explicações sobre as propriedades de atributo e os parâmetros do construtor.
* [Uso](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) - A quais tipos você pode associar e informações sobre como a associação funciona. Por exemplo: algoritmo de sondagem, processamento de fila de mensagens suspeita.
  
Para obter uma lista de artigos de referência de associação, consulte **Associações suportadas** no artigo [Gatilhos e associações](../azure-functions/functions-triggers-bindings.md#supported-bindings) para Azure Functions. Nessa lista, as associações de HTTP, webhook e Grade de Eventos são suportadas somente pelo Azure Functions, não pelo WebJobs SDK.

## <a name="disable-attribute"></a>Atributo Desabilitar 

O atributo [Desabilitar](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) permite controlar se uma função pode ser disparada. 

No exemplo a seguir, se a configuração do aplicativo `Disable_TestJob` tiver um valor de "1" ou "Verdadeiro" (sem distinção entre maiúsculas e minúsculas), a função não será executada. Nesse caso, o tempo de execução cria uma mensagem de log *A função 'Functions.TestJob' está desabilitada*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando você altera os valores de configuração do aplicativo no portal do Azure, isso faz com que o WebJob seja reiniciado, obtendo a nova configuração.

O atributo pode ser declarado no nível de classe, método ou parâmetro. O nome da configuração pode também conter expressões de associação.

## <a name="timeout-attribute"></a>Atributo de tempo limite

O atributo [Tempo Limite](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) faz com que uma função seja cancelada se ela não for concluída dentro de um período especificado. No exemplo a seguir, a função seria executado por um dia sem o tempo limite. Com o tempo limite, a função é cancelada após 15 segundos.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Você pode aplicar o atributo Tempo Limite no nível de classe ou método e especificar um tempo limite global usando `JobHostConfiguration.FunctionTimeout`. Os tempos limite no nível de classe ou método substituem o tempo limite global.

## <a name="singleton-attribute"></a>Atributo Singleton

Use o atributo [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) para garantir que apenas uma instância de uma função seja executada, mesmo quando houver várias instâncias do aplicativo Web host. Ele faz isso implementando o [bloqueio distribuído](#viewing-lease-blobs).

No exemplo a seguir, apenas uma única instância da função `ProcessImage` é executada em um determinado momento:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Alguns gatilhos têm suporte interno para gerenciamento de simultaneidade:

* **QueueTrigger** - Defina `JobHostConfiguration.Queues.BatchSize` como 1.
* **ServiceBusTrigger** - Defina `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` como 1.
* **FileTrigger** - Defina `FileProcessor.MaxDegreeOfParallelism` como 1.

Você pode usar essas configurações para garantir que sua função seja executada como um singleton em uma única instância. Para garantir que apenas uma única instância da função esteja em execução quando o aplicativo Web for expandido para várias instâncias, aplique um bloqueio de Singleton em nível de ouvinte na função (`[Singleton(Mode = SingletonMode.Listener)]`). Os bloqueios de ouvinte são adquiridos durante a inicialização do JobHost. Se três instâncias expandidas forem iniciadas ao mesmo tempo, somente uma das instâncias adquirirá o bloqueio e somente um ouvinte será iniciado.

### <a name="singletonscopehost"></a>SingletonScope.Host

O escopo padrão para um bloqueio é `SingletonScope.Function`, o que significa que o escopo de bloqueio (o caminho de concessão de blob) está vinculado ao nome da função totalmente qualificado. Para bloquear funções, especifique `SingletonScope.Host` e use um nome de ID de escopo que é o mesmo em todas as funções que você não deseja executar simultaneamente. No exemplo a seguir, apenas uma instância de `AddItem` ou `RemoveItem` é executada por vez:

```charp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>Exibição de blobs de concessão

O WebJobs SDK usa [concessões de blob do Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) nos bastidores para implementar o bloqueio distribuído. Os blobs de concessão usados por Singleton podem ser encontrados no contêiner `azure-webjobs-host` na conta de armazenamento `AzureWebJobsStorage` nos "bloqueios" de caminho. Por exemplo, o caminho de blob de concessão para o primeiro exemplo `ProcessImage` mostrado anteriormente pode ser `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Todos os caminhos incluem a ID de JobHost, 061851c758f04938a4426aa9ab3869c0 neste caso.

## <a name="async-functions"></a>Funções assíncronas

Para obter informações sobre como codificar funções assíncronas, consulte a documentação do Azure Functions em [Funções Assíncronas](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Para obter informações sobre como lidar com tokens de cancelamento, consulte a documentação do Azure Functions nos [tokens de cancelamento e desligamento normal](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Várias instâncias

Se o seu aplicativo Web for executado em várias instâncias, um WebJob contínuo será executado em cada instância, ouvindo os gatilhos e chamando funções. As várias associações de gatilho são projetadas para compartilhar com eficiência o trabalho de forma colaborativa entre instâncias, para que a expansão para mais instâncias permita que você manipule mais carga.

Os gatilhos de fila e blob impedem automaticamente que uma função processe uma mensagem da fila ou blob mais de uma vez; as funções não precisam ser idempotentes.

O gatilho de timer garante que apenas uma instância do timer seja executada, portanto você não terá mais de uma instância de função em execução em um determinado horário agendado.

Se desejar garantir que apenas uma instância de uma função é executada, mesmo quando houver várias instâncias do aplicativo Web host, é possível usar o atributo [Singleton](#singleton).
    
## <a name="filters"></a>Filtros 

Filtros de função (visualização) fornecem uma maneira de personalizar o pipeline de execução de WebJobs com sua própria lógica. Os filtros são semelhantes aos [Filtros ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Eles podem ser implementados como atributos declarativos que são aplicados às suas funções ou classes. Para obter mais informações, consulte [Filtros de Função](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Log e monitoramento

Recomendamos a estrutura de log que foi desenvolvida para ASP.NET, e o artigo [Introdução](webjobs-sdk-get-started.md) mostra como usá-la. 

### <a name="log-filtering"></a>Filtragem de linha

Cada log criado por uma instância de `ILogger` possui um `Category` e `Level` associados. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) é uma enumeração e o código inteiro indica a importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreamento       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Cada categoria pode ser filtrada independentemente para um determinado [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel). Por exemplo, você talvez queira ver todos os logs para o processamento de gatilho de blob, mas apenas `Error` e superiores para todo o resto.

Para tornar mais fácil especificar regras de filtragem, o WebJobs SDK fornece o `LogCategoryFilter` que pode ser transferido para muitos dos provedores de log existentes, incluindo o Application Insights e o Console.

O `LogCategoryFilter` tem uma propriedade `Default` com um valor inicial de `Information`, o que significa que as mensagens com níveis de `Information`, `Warning`, `Error` ou `Critical` são registradas em log, mas as mensagens com níveis de `Debug` ou `Trace` são filtradas.

A propriedade `CategoryLevels` permite que você especifique níveis de log para categorias específicas, de modo que você possa ajustar a saída de log. Se nenhuma correspondência for encontrada no dicionário `CategoryLevels`, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no nível de `Warning`. As categorias de `Function` ou `Host.Results` são filtradas no nível de `Error`. O `LogCategoryFilter` compara a categoria atual com todos os `CategoryLevels` registrados e escolhe a maior correspondência. Isso significa que o nível de `Debug` registrado para `Host.Triggers` corresponderá a `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Telemetria personalizada do Application Insights​

Internamente, o `TelemetryClient` criado pelo provedor do Application Insights para o WebJobs SDK usa o [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Quando o ponto de extremidade do Application Insights está indisponível ou limitando solicitações de entrada, este canal [salva as solicitações no sistema de arquivos do aplicativo Web e reenvia-as depois](http://apmtips.com/blog/2015/09/03/more-telemetry-channels).

O `TelemetryClient` é criado por uma classe que implementa `ITelemetryClientFactory`. Por padrão, esse é o [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Se quiser modificar qualquer parte do pipeline do Application Insights, você poderá fornecer seu próprio `ITelemetryClientFactory`, e o host usará sua classe para construir um `TelemetryClient`. Por exemplo, esse código substitui o `DefaultTelemetryClientFactory` para modificar uma propriedade do `ServerTelemetryChannel`:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

O objeto SamplingPercentageEstimatorSettings configura a [amostragem adaptável](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Isso significa que, em determinados cenários de alto volume, o App Insights envia um subconjunto selecionado de dados de telemetria para o servidor.

Depois de criar o alocador de telemetria, transfira-o para o provedor de logs do Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Próximas etapas

Este guia forneceu trechos de código que mostram como lidar com cenários comuns para trabalhar com o WebJobs SDK. Para obter exemplos completos, consulte [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).