---
title: "Referência do desenvolvedor do Azure Functions | Microsoft Docs"
description: Entenda como desenvolver no Azure Functions usando NodeJS.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: f28cda01-15f3-4047-83f3-e89d5728301c
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/04/2017
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 76b43c78341abb638e2ede97f68c05ee6df0700f
ms.lasthandoff: 04/17/2017


---
# <a name="azure-functions-c-developer-reference"></a>Referência do desenvolvedor de C# do Azure Functions
> [!div class="op_single_selector"]
> * [Script C#](functions-reference-csharp.md)
> * [Script em F#](functions-reference-fsharp.md)
> * [Node.js](functions-reference-node.md)
> 
> 

A experiência do C# para Azure Functions baseia-se no SDK do Azure WebJobs. Fluxos de dados na sua função C# por meio de argumentos de método. Os nomes de argumentos são especificados em `function.json`e há nomes predefinidos para acessar itens como a função logger e os tokens de cancelamento.

Este artigo pressupõe que você já tenha lido a [referência do desenvolvedor do Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Como o .csx funciona
O formato `.csx` permite escrever menos “texto clichê” e ter como foco apenas uma função do C#. Para o Azure Functions, inclua as referências de assembly e namespaces que você precisa na parte superior, como de costume, e em vez de encapsular tudo em um namespace e classe, você pode apenas definir seu método `Run` . Se precisar incluir alguma classe, por exemplo, para definir objetos POCO (objeto CRL básico), você poderá incluir uma classe no mesmo arquivo.   

## <a name="binding-to-arguments"></a>Binding para argumentos
Vários bindings estão vinculados a uma função C# por meio da propriedade `name` na configuração *function.json* . Cada binding tem seus próprios tipos com suporte, que estão documentados por binding; por exemplo, um gatilho de blob pode dar suporte a cadeia de caracteres, POCO e vários outros tipos. Você pode usar o tipo que melhor atenda às suas necessidades. Um objeto POCO deve ter um getter e setter definido para cada propriedade. 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

> [!TIP]
>
> Se planeja usar as associações WebHook ou HTTP, sugerimos que você leia este documento de práticas recomendadas sobre [HTTPClient](https://github.com/mspnp/performance-optimization/blob/master/ImproperInstantiation/docs/ImproperInstantiation.md).
>

## <a name="logging"></a>Registro em log
Para registrar em log a saída nos seus logs de streaming em C#, você pode incluir um argumento tipado `TraceWriter` . Recomendamos nomeá-lo como `log`. É recomendável evitar `Console.Write` no Azure Functions.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Assíncrono
Para tornar uma função assíncrona, use a palavra-chave `async` e retorne um objeto `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>Token de cancelamento
Em alguns casos, você pode ter operações que são sensíveis ao desligamento. Embora seja sempre melhor escrever um código que possa lidar com falhas, nos casos em que você quiser lidar com as solicitações de desligamento normal, é melhor definir um argumento tipado [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx).  Um `CancellationToken` será fornecido se o desligamento do host for disparado. 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importando namespaces
Se precisar importar namespaces, você poderá fazer como geralmente faz, com a cláusula `using` .

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Os seguintes namespaces são automaticamente importados e, portanto, são opcionais:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referenciando Assemblies Externos
Para assemblies da estrutura, adicione referências usando a diretiva `#r "AssemblyName"` .

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Os seguintes assemblies são adicionados automaticamente pelo ambiente de hospedagem do Azure Functions:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Além disso, os seguintes assemblies têm regras de maiúsculas e minúsculas especiais e podem ser referenciados por simplename (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Se precisar fazer referência a um assembly particular, carregue o arquivo do assembly em uma pasta `bin` relativa à sua função e faça referência a ela usando o nome do arquivo (por exemplo, `#r "MyAssembly.dll"`). Para obter informações sobre como carregar arquivos na pasta da função, consulte a seção a seguir sobre gerenciamento de pacotes.

## <a name="package-management"></a>Gerenciamento de pacote
Para usar os pacotes do NuGet em uma função C#, carregue um arquivo *project.json* na pasta da função, no sistema de arquivos do aplicativo de funções. Aqui está um arquivo *project.json* de exemplo que adiciona uma referência à versão 1.1.0 do Microsoft.ProjectOxford.Face:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Somente o .NET Framework 4.6 tem suporte. Desse modo, tenha certeza de que o arquivo *project.json* especifica `net46`, como mostrado aqui.

Quando você carrega um arquivo *project.json* , o tempo de execução obtém os pacotes e adiciona referências automaticamente aos assemblies do pacote. Você não precisa adicionar diretivas `#r "AssemblyName"` . Basta adicionar as instruções `using` obrigatórias ao arquivo *run.csx* para usar os tipos definidos nos pacotes NuGet.

No tempo de execução do Functions, a restauração do NuGet funciona comparando `project.json` e `project.lock.json`. Se os carimbos de data/hora dos arquivos não forem correspondentes, uma restauração do NuGet será executada e o NuGet baixará os pacotes atualizados. No entanto, se os carimbos de data/hora dos arquivos forem correspondentes, o NuGet não executará nenhuma restauração. Portanto, `project.lock.json` não deve ser implantado, pois isso faz com que o NuGet ignore a restauração e a função não terá os pacotes necessários. Para evitar a implantação do arquivo de bloqueio, adicione o `project.lock.json` ao arquivo `.gitignore`.

### <a name="how-to-upload-a-projectjson-file"></a>Como carregar um arquivo project.json
1. Comece verificando se o aplicativo está em execução, o que pode ser feito abrindo a função no portal do Azure. 
   
    Isso também permite acessar os logs de streaming nos quais a saída da instalação do pacote será exibida. 
2. Para carregar um arquivo project.json, use um dos métodos descritos na seção **Como atualizar os arquivos de aplicativo de funções** do [tópico Referência do desenvolvedor do Azure Functions](functions-reference.md#fileupdate). 
3. Depois que o arquivo *project.json* for carregado, você verá a saída como o exemplo a seguir no log de streaming da sua função:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261 
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variáveis de ambiente
Para obter uma variável de ambiente ou um valor de configuração do aplicativo, use `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo de código a seguir:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>Reutilização de código .csx
Você pode usar classes e métodos definidos em outros arquivos *.csx* no seu arquivo *run.csx*. Para fazer isso, use diretivas `#load` no arquivo *run.csx*. No exemplo a seguir, uma rotina de log chamada `MyLogger` é compartilhada em *myLogger.csx* e carregada em *run.csx* usando a diretiva `#load`: 

Exemplo de *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemplo de *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

Usar um *.csx* compartilhado é um padrão comum quando você desejar tipar fortemente os argumentos entre as funções usando um objeto POCO. No exemplo simplificado a seguir, um gatilho HTTP e o gatilho da fila compartilham um objeto POCO chamado `Order` para tipar fortemente os dados do pedido:

Exemplo *run.csx* para o gatilho HTTP:

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Exemplo *run.csx* para o gatilho da fila:

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Exemplo *order.csx*: 

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId + 
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

Você pode usar um caminho relativo com a diretiva `#load` :

* `#load "mylogger.csx"` carrega um arquivo localizado na pasta de função.
* `#load "loadedfiles\mylogger.csx"` carrega um arquivo localizado em uma pasta na pasta de função.
* `#load "..\shared\mylogger.csx"` carrega um arquivo localizado em uma pasta no mesmo nível que a pasta de função, ou seja, diretamente em *wwwroot*.

A diretiva `#load` só funciona com arquivos *.csx* (script C#), e não com arquivos *.cs*. 

## <a name="versioning"></a>Controle de versão

O tempo de execução do Functions é executado como uma extensão de site para seu aplicativo de funções. Extensões de site são pontos de extensibilidade que permitem que você adicione recursos a um serviço de aplicativo do Azure, site ou aplicativo de funções. `Kudu` e `Monaco` são dois exemplos de extensões de site, e você também pode criar e usar as extensões personalizadas. Você pode configurar a versão das extensões usando a configuração do aplicativo `FUNCTIONS_EXTENSION_VERSION`.

O `FUNCTIONS_EXTENSION_VERSION` apenas define a versão principal do tempo de execução. Por exemplo, o valor "~1" indica que seu aplicativo de funções usará 1 como sua versão principal. Aplicativos de funções são atualizados para cada nova versão secundária à medida que elas são lançadas. Isso permite que você gerencie quando atualizar para versões para evitar alterações significativas.

Além disso, convém atualizar o tempo de execução antes de se tornar a versão padrão no portal. Contudo, não se preocupe, pois é possível reverter a qualquer momento revertendo a configuração `FUNCTIONS_EXTENSION_VERSION` para seu valor antigo.

*Para determinar a versão de tempo de execução do aplicativo de funções do Azure:*

Localize o arquivo `applicationhost.config` localizado na pasta `D:\local\Config` do Kudu. A entrada `virtualDirectory` revela a versão exata de tempo de execução do Functions: 

```xml
<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Functions\0.8.10564" />
```
Você pode usar esse valor para definir uma versão de tempo de execução principal e secundária específicas do seu aplicativo de funções. Sempre que você alterar a versão de um aplicativo de funções, será necessário reiniciá-lo.

## <a name="advanced-binding-at-runtime-imperative-binding"></a>Associação avançada em tempo de execução (associação obrigatória)

No C#, e em outras linguagens .NET, você pode usar um padrão de associação [obrigatório](https://en.wikipedia.org/wiki/Imperative_programming), em vez de associações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em *function.json*. A associação obrigatória é útil quando os parâmetros de associação precisam ser calculado no tempo de execução, em vez do tempo de design. Com esse padrão, associe a qualquer número de associações de entrada e saída com suporte imediatamente no código da função.

Defina uma associação obrigatória da seguinte maneira:

- **Não** inclua uma entrada em *function.json* para as associações obrigatórias desejadas.
- Passe um parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs). 
- Use o padrão de C# a seguir para realizar a associação de dados.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

em que `BindingTypeAttribute` é o atributo do .NET que define a associação e `T` é o tipo de entrada ou saída com suporte nesse tipo de associação. `T` também não pode ser um tipo de parâmetro `out` (como `out JObject`). Por exemplo, a associação de saída de tabela dos Aplicativos Móveis dá suporte a [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas você só pode usar [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) para `T`.
    
O código de exemplo a seguir cria uma [associação de saída do Armazenamento de Blobs](functions-bindings-storage-blob.md#storage-blob-output-binding) com o caminho do blob definido em tempo de execução e grava uma cadeia de caracteres no blob.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define a associação de entrada ou saída do [Armazenamento de Blobs](functions-bindings-storage-blob.md) e [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) é um tipo de associação de saída com suporte.
No estado em que se encontra, o código obtém a configuração de aplicativo padrão para a cadeia de conexão da conta de armazenamento (`AzureWebJobsStorage`). Especifique uma configuração de aplicativo personalizada a ser usada adicionando [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando a matriz de atributo para `BindAsync<T>()`. Por exemplo,

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

A tabela a seguir lista os atributos .NET para cada tipo de associação e os pacotes no qual eles são definidos. 

> [!div class="mx-codeBreakAll"]
| Associação | Atributo | Adicionar referência |
|------|------|------|
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Hubs de Eventos | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Aplicativos Móveis | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Hubs de Notificação | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Barramento de Serviço | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Fila de armazenamento | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Armazenamento de blobs | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Tabela de armazenamento | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Referência do desenvolvedor em F# do Azure Functions](functions-reference-fsharp.md)
* [Referência do desenvolvedor de NodeJS do Azure Functions](functions-reference-node.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)


