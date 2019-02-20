---
title: Como usar o SDK WebJobs - Azure
description: Saiba mais sobre como escrever código para o WebJobs SDK. Crie trabalhos de processamento em segundo plano controlado por evento que acessam dados nos serviços de terceiros e serviços do Azure.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/19/2019
ms.author: glenga
ms.openlocfilehash: ab502c25a632977065e55d2eeafd684203636b14
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109904"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Como usar o SDK do Azure WebJobs para o processamento em segundo plano controlado por evento

Este artigo fornece orientação sobre como escrever código para o [SDK do Azure WebJobs](webjobs-sdk-get-started.md). A documentação se aplica à versão 3.x e 2.x do WebJobs SDK. Serão exibidos exemplos caso surjam diferenças de API. A principal mudança introduzida pela versão 3.x é o uso do .NET Core em vez do .NET Framework.

>[!NOTE]
> O [Azure Functions](../azure-functions/functions-overview.md) é compilado no SDK do WebJobs, e este artigo apresenta links para a documentação do Azure Functions para alguns tópicos. Observe as seguintes diferenças entre o Functions e o WebJobs SDK:
> * O Azure Functions versão 2.x corresponde ao WebJobs SDK versão 3.x, e o Azure Functions 1.x corresponde ao WebJobs SDK 2.x. Os repositórios de código-fonte seguem a numeração do SDK WebJobs.
> * O código de exemplo para bibliotecas de classe C# do Azure Functions é como o código do WebJobs SDK, exceto que não é necessário um atributo `FunctionName` em um projeto do WebJobs SDK.
> * Alguns tipos de associação são suportados apenas no Functions, como HTTP, webhook e Grade de Eventos (que é baseado em HTTP).
> 
> Para obter mais informações, consulte [Comparar o WebJobs SDK e o Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha lido e concluído as tarefas na [Introdução ao WebJobs SDK](webjobs-sdk-get-started.md).

## <a name="webjobs-host"></a>Host do WebJobs

O host é um contêiner de tempo de execução para funções.  Ele escuta gatilhos e chamadas de funções. Na versão 3.x, o host é uma implementação de `IHost` e, na versão 2.x, você usa o objeto `JobHost`. Você cria uma instância do host em seu código e escreve um código para personalizar seu comportamento.

Essa é uma diferença importante entre usar diretamente o WebJobs SDK e usá-lo indiretamente utilizando o Azure Functions. No Azure Functions, o serviço controla o host e não é possível personalizá-lo gravando o código. O Azure Functions permite que você personalize o comportamento de host por meio de configurações no arquivo *host.json*. Essas configurações são cadeias de caracteres, não código, que limitam os tipos de personalizações que você pode fazer.

### <a name="host-connection-strings"></a>Cadeias de conexão do host 

O SDK do WebJobs procura cadeias de conexão de Armazenamento do Azure e Barramento de Serviço do Azure no arquivo *local.settings.json* quando você executa localmente ou no ambiente do WebJob quando você executa no Azure. Por padrão, uma configuração de cadeia de conexão de armazenamento denominada `AzureWebJobsStorage` é obrigatória.  

A versão 2.x do SDK permite usar seus próprios nomes para essas cadeias de conexão ou armazená-las em outro lugar. Você pode defini-las no código, conforme mostrado aqui:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Como ela usa as APIs de configuração padrão do .NET Core, não há APIs na versão 3.x para alterar os nomes das cadeias de conexão.

### <a name="host-development-settings"></a>Configurações de desenvolvimento do host

Você pode executar o host no modo de desenvolvimento para tornar o desenvolvimento local mais eficiente. Veja algumas configurações que são alteradas durante a execução no modo de desenvolvimento:

| Propriedade | Configuração de desenvolvimento |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` para maximizar a saída de log. |
| `Queues.MaxPollingInterval`  | Um valor baixo para garantir que os métodos de fila sejam disparados imediatamente.  |
| `Singleton.ListenerLockPeriod` | Quinze segundos para ajudar no desenvolvimento iterativo rápido. |

A maneira como você ativa o modo de desenvolvimento depende da versão do SDK. 

#### <a name="version-3x"></a>Versão 3.x

A versão 3.x usa as APIs padrão do ASP.NET Core. Chame o método [UseEnvironment](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) na instância [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Passe uma cadeia de caracteres denominada `development`, conforme mostrado no exemplo a seguir:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2.x

A classe `JobHostConfiguration` tem um método `UseDevelopmentSettings` que habilita o modo de desenvolvimento.  O exemplo a seguir mostra como usar as configurações de desempenho. Para fazer `config.IsDevelopment` retornar `true` quando em execução localmente, defina uma variável de ambiente local denominada `AzureWebJobsEnv` com o valor `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Gerenciar conexões simultâneas (v2.x)

Na versão 3.x, o limite de conexão é padronizado para conexões infinitas. Se por algum motivo você precisar alterar esse limite, use a propriedade [MaxConnectionsPerServer](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) da classe [WinHttpHander](/dotnet/api/system.net.http.winhttphandler).

Para a versão 2.x, você controla o número de conexões simultâneas para um host usando a API [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit). Na 2.x, é recomendável aumentar esse valor a partir do padrão 2 antes de iniciar o host do WebJobs.

Todas as solicitações HTTP de saída feitas por meio de uma função usando o fluxo de `HttpClient` por meio de `ServicePointManager`. Quando você atingir `DefaultConnectionLimit`, `ServicePointManager` iniciará as solicitações de enfileiramento antes de enviá-las. Suponha que seu `DefaultConnectionLimit` seja definido como 2 e seu código faça 1.000 solicitações HTTP. Inicialmente, apenas duas solicitações são permitidas por meio do sistema operacional. As outras 998 são colocadas na fila até que haja espaço para elas. Isso significa que `HttpClient` pode atingir o tempo limite, porque ele *considera* ele fez a solicitação, mas a solicitação nunca foi enviada pelo sistema operacional para o servidor de destino. Para que você possa ver o comportamento que não faz sentido: o `HttpClient` local está demorando 10 segundos para concluir uma solicitação, mas o serviço está retornando cada solicitação em 200 ms. 

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
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

A maneira que você aciona manualmente a função depende da versão do SDK.

#### <a name="version-3x"></a>Versão 3.x

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2.x

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Associações de entrada e saída

As associações de entrada e saída fornecem uma maneira declarativa para criar dados a partir do Azure ou de serviços externos disponíveis para seu código. As associações de saída fornecem uma maneira de atualizar os dados. O [artigo Introdução](webjobs-sdk-get-started.md) mostra um exemplo de cada um.

Você pode usar um valor de retorno do método para uma associação de saída, aplicando o atributo ao valor de retorno do método. Consulte o exemplo no artigo [Gatilhos e associações](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) do Azure Functions.

## <a name="binding-types"></a>Tipos de associação

A maneira como os tipos de associação são instalados e gerenciados difere entre a versão 3.x e 2.x do SDK. Você pode encontrar o pacote de instalação para um tipo específico de associação na seção **Pacotes** do [artigo de referência](#binding-reference-information) desse tipo de associação para o Azure Functions. Uma exceção é o gatilho e a associação de Arquivos (para o sistema de arquivos local), o que não é suportado pelo Azure Functions.

#### <a name="version-3x"></a>Versão 3.x

Na versão 3.x, as associações de armazenamento são incluídas no pacote `Microsoft.Azure.WebJobs.Extensions.Storage`. Chame o método de extensão `AddAzureStorage` no método `ConfigureWebJobs`, conforme mostrado no exemplo a seguir:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Para usar outros tipos de gatilho e associação, instale o pacote do NuGet que os contém e chame o método de extensão `Add<binding>` implementado na extensão. Por exemplo, se você quiser usar uma associação do Azure Cosmos DB, instale `Microsoft.Azure.WebJobs.Extensions.CosmosDB` e chame `AddCosmosDB`, como no exemplo a seguir:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Para usar o gatilho de Temporizador ou a associação de Arquivos, que fazem parte dos serviços principais, chame os métodos de extensão `AddTimers` ou `AddFiles`, respectivamente.

#### <a name="version-2x"></a>Versão 2.x

Os seguintes tipos de gatilho e associação estão incluídos na versão 2.x do pacote `Microsoft.Azure.WebJobs`:

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

Para usar a associação de Arquivos, instale `Microsoft.Azure.WebJobs.Extensions` e chame `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

O WebJobs permite que você se associe a um [`ExecutionContext`]. Com essa associação, você pode acessar o [`ExecutionContext`] como um parâmetro em sua assinatura de função. Por exemplo, o código a seguir usa o objeto de contexto para acessar a ID de invocação, que você pode usar para correlacionar todos os logs produzidos por uma determinada chamada de função.  

```cs
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

A maneira de se associar ao [`ExecutionContext`] depende da versão do SDK.

#### <a name="version-3x"></a>Versão 3.x

Chame o método de extensão `AddExecutionContextBinding` no método `ConfigureWebJobs`, conforme mostrado no exemplo a seguir:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2.x

O pacote `Microsoft.Azure.WebJobs.Extensions` mencionado anteriormente também fornece um tipo especial de associação que você pode registrar chamando o método `UseCore`. Essa associação permite que você defina um parâmetro [`ExecutionContext`] em sua assinatura de função, que é habilitada da seguinte maneira:

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
```

## <a name="binding-configuration"></a>Configuração de associação

Alguns gatilhos e associações permitem configurar o comportamento deles. A maneira como você os configura depende da versão do SDK.

* **Versão 3.x:** a configuração é definida quando o método `Add<Binding>` é chamado em `ConfigureWebJobs`.
* **Versão 2.x:** definindo propriedades em um objeto de configuração que você transmite para o `JobHost`.

### <a name="queue-trigger-configuration"></a>Configuração de gatilho de fila

As configurações que você pode definir para o gatilho de fila de Armazenamento são explicadas na [referência host.json](../azure-functions/functions-host-json.md#queues) do Azure Functions. Os exemplos a seguir mostram como configurá-las em sua configuração:

#### <a name="version-3x"></a>Versão 3.x

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2.x

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

Alguns tipos de gatilho e associação definem seu próprio tipo de configuração personalizado. Por exemplo, o gatilho de Arquivo permite especificar o caminho raiz de monitoramento, conforme mostrado nos exemplos a seguir:

#### <a name="version-3x"></a>Versão 3.x

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2.x

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

Este código lhe permite usar uma fila denominada `logqueuetest` no ambiente de teste e uma denominada `logqueueprod` na produção. Em vez de um nome de fila embutido em código, você especifica o nome de uma entrada na coleção `appSettings`.

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

#### <a name="version-3x"></a>Versão 3.x

O resolvedor é configurado usando a injeção de dependência. Esses exemplos exigem a seguinte instrução `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

O resolvedor é adicionado chamando o método de extensão [`ConfigureServices`] em [HostBuilder](/dotnet/api/microsoft.extensions.hosting.hostbuilder), conforme mostrado no exemplo a seguir:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2.x

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
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Para obter mais informações, consulte [Associação no tempo de execução](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) na documentação do Azure Functions.

## <a name="binding-reference-information"></a>Informações de referência de associação

Informações de referência sobre cada tipo de associação são fornecidas na documentação do Azure Functions. Usando a fila de Armazenamento como um exemplo, você encontrará as informações a seguir em cada artigo de referência de associação:

* [Pacotes](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) -Qual pacote instalar para incluir suporte para a associação em um projeto do WebJobs SDK.
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

### <a name="scope-values"></a>Valores de escopo

Você pode especificar um **valor/expressão de escopo** no Singleton que garantirá que todas as execuções da função nesse escopo serão serializadas. Implementar um bloqueio mais granular dessa maneira pode permitir algum nível de paralelismo para sua função e, ao mesmo tempo, serializar outras invocações, conforme definido pelos seus requisitos. No exemplo a seguir, a expressão de escopo associa-se ao valor `Region` da mensagem recebida. Quando a fila contiver três mensagens nas regiões "East", "East" e "West" respectivamente, então, as mensagens com a região "East" serão executadas em série enquanto a mensagem com a região "West" serão executadas em paralelo com essas em "East".

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

O escopo padrão para um bloqueio é `SingletonScope.Function`, o que significa que o escopo de bloqueio (o caminho de concessão de blob) está vinculado ao nome da função totalmente qualificado. Para bloquear funções, especifique `SingletonScope.Host` e use um nome de ID de escopo que é o mesmo em todas as funções que você não deseja executar simultaneamente. No exemplo a seguir, apenas uma instância de `AddItem` ou `RemoveItem` é executada por vez:

```csharp
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

Se desejar garantir que apenas uma instância de uma função é executada, mesmo quando houver várias instâncias do aplicativo Web host, é possível usar o [atributo Singleton](#singleton-attribute).

## <a name="filters"></a>Filtros

Filtros de função (visualização) fornecem uma maneira de personalizar o pipeline de execução de WebJobs com sua própria lógica. Os filtros são semelhantes aos [Filtros ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Eles podem ser implementados como atributos declarativos que são aplicados às suas funções ou classes. Para obter mais informações, consulte [Filtros de Função](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Log e monitoramento

Recomendamos a estrutura de log que foi desenvolvida para ASP.NET, e o artigo [Introdução](webjobs-sdk-get-started.md) mostra como usá-la. 

### <a name="log-filtering"></a>Filtragem de linha

Cada log criado por uma instância de `ILogger` possui um `Category` e `Level` associados. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração e o código inteiro indica a importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreamento       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Cada categoria pode ser filtrada independentemente para um determinado [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel). Por exemplo, você talvez queira ver todos os logs para o processamento de gatilho de blob, mas apenas `Error` e superiores para todo o resto.

#### <a name="version-3x"></a>Versão 3.x

A versão 3.x do SDK depende da filtragem integrada no .NET Core. A classe `LogCategories` permite que você defina categorias para funções, gatilhos e usuários específicos. Ela também define filtros para estados de host específicos, como `Startup` e `Results`. Dessa forma, você pode ajustar a saída de log. Se nenhuma correspondência for encontrada nas categorias definidas, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

`LogCategories` requer a seguinte declaração de uso:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no nível de `Warning`. As categorias de `Function` ou `results` (equivalente a `Host.Results` na versão 2.x) são filtradas no nível de `Error`. O filtro compara a categoria atual com todos os níveis registrados na instância `LogCategories` e escolhe a maior correspondência. Isso significa que o nível de `Debug` registrado para `Host.Triggers` corresponderá a `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2.x

Na versão 2.x do SDK, a classe `LogCategoryFilter` é usada para controlar a filtragem. O `LogCategoryFilter` tem uma propriedade `Default` com um valor inicial de `Information`, o que significa que as mensagens com níveis de `Information`, `Warning`, `Error` ou `Critical` são registradas em log, mas as mensagens com níveis de `Debug` ou `Trace` são filtradas.

Tal como acontece com `LogCategories` na versão 23.x, a propriedade `CategoryLevels` permite que você especifique níveis de log para categorias específicas para que você possa ajustar a saída de log. Se nenhuma correspondência for encontrada no dicionário `CategoryLevels`, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

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

A maneira de implementar a telemetria personalizada para o [Application Insights](../azure-monitor/app/app-insights-overview.md) dependerá da versão do SDK que você estiver usando. Para saber como configurar o Application Insights, confira [Adicionar registro em log do Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versão 3.x

Como a versão 3.x do SDK do WebJobs depende do host genérico do .NET Core, um alocador de telemetria personalizada já não é oferecido. No entanto, você pode adicionar telemetria personalizada ao pipeline usando a injeção de dependência. Os exemplos nesta seção exigem as seguintes declarações `using`:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A seguinte implementação personalizada de [`ITelemetryInitializer`] permite que você adicione seu próprio [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) ao padrão [`TelemetryConfiguration`].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Chame [`ConfigureServices`] no construtor para adicionar seu item personalizado [`ITelemetryInitializer`] ao pipeline.

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add Logging Providers
        b.AddConsole();

        // If this key exists in any config, use it to enable App Insights
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Quando o [`TelemetryConfiguration`] é construído, todos os tipos registrados de [`ITelemetryInitializer`] são incluídos. Para saber mais sobre como trabalhar, confira [API do Application Insights para métricas e eventos personalizados](../azure-monitor/app/api-custom-events-metrics.md).

Na versão 3.x, você já não precisará liberar o [`TelemetryClient`] quando o host parar. O sistema de injeção de dependência do .NET Core automaticamente descarta o `ApplicationInsightsLoggerProvider` registrado que libera o [`TelemetryClient`].

#### <a name="version-2x"></a>Versão 2.x

Na versão 2.x, o [`TelemetryClient`] criado internamente pelo provedor do Application Insights para o WebJobs SDK usa o [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Quando o ponto de extremidade do Application Insights está indisponível ou limitando solicitações de entrada, este canal [salva as solicitações no sistema de arquivos do aplicativo Web e reenvia-as depois](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

O [`TelemetryClient`] é criado por uma classe que implementa `ITelemetryClientFactory`. Por padrão, é [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Se quiser modificar qualquer parte do pipeline do Application Insights, você poderá fornecer seu próprio `ITelemetryClientFactory` e o host usará sua classe para construir um [`TelemetryClient`]. Por exemplo, esse código substitui o `DefaultTelemetryClientFactory` para modificar uma propriedade do `ServerTelemetryChannel`:

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

Este guia forneceu snippets de código que mostram como lidar com cenários comuns para trabalhar com o WebJobs SDK. Para obter exemplos completos, consulte [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
