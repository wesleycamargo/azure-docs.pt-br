<properties
	pageTitle="Referência do desenvolvedor do Azure Functions | Microsoft Azure"
	description="Entenda como desenvolver no Azure Functions usando NodeJS."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
	ms.service="functions"
	ms.devlang="dotnet"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Referência do desenvolvedor de C# do Azure Functions

> [AZURE.SELECTOR]
- [Script C#](../articles/azure-functions/functions-reference-csharp.md)
- [Script em F#](../articles/azure-functions/functions-reference-fsharp.md)
- [Node.js](../articles/azure-functions/functions-reference-node.md)
 
A experiência do C# para Azure Functions baseia-se no SDK do Azure WebJobs. Fluxos de dados na sua função C# por meio de argumentos de método. Os nomes de argumentos são especificados em `function.json` e há nomes predefinidos para acessar itens como a função logger e os tokens de cancelamento.

Este artigo pressupõe que você já tenha lido a [referência do desenvolvedor do Azure Functions](functions-reference.md).

## Como o .csx funciona

O formato `.csx` permite escrever menos "clichê" e se concentrar em escrever apenas uma função C#. Para o Azure Functions, inclua as referências de assembly e namespaces que você precisa na parte superior, como de costume, e em vez de encapsular tudo em um namespace e classe, você pode apenas definir seu método `Run`. Se você precisar incluir alguma classe, por exemplo, para definir objetos POCO, você poderá incluir uma classe dentro do mesmo arquivo.

## Binding para argumentos

Vários bindings estão vinculados a uma função C# por meio da propriedade `name` na configuração *function.json*. Cada binding tem seus próprios tipos com suporte, que estão documentados por binding; por exemplo, um gatilho de blob pode dar suporte a cadeia de caracteres, POCO e vários outros tipos. Você pode usar o tipo que melhor atenda às suas necessidades.

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

## Registro em log

Para registrar em log a saída nos seus logs de streaming em C#, você pode incluir um argumento tipado `TraceWriter`. Recomendamos nomeá-lo como `log`. É recomendável evitar `Console.Write` no Azure Functions.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## Assíncrono

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

## Token de cancelamento

Em alguns casos, você pode ter operações que são sensíveis ao desligamento. Embora seja sempre melhor escrever um código que possa lidar com falhas, nos casos em que você quiser manipular as solicitações de desligamento, é melhor definir um argumento tipado [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx). Um `CancellationToken` será fornecido se o desligamento do host for disparado.

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

## Importando namespaces

Se precisar importar namespaces, você poderá fazer como geralmente faz, com a cláusula `using`.

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

## Referenciando Assemblies Externos

Para assemblies da estrutura, adicione referências usando a diretiva `#r "AssemblyName"`.

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
* `Microsoft.AspNEt.WebHooks.Common`.

Se precisar fazer referência a um assembly particular, você poderá carregar o arquivo do assembly em uma pasta `bin` relativa à sua função e fazer referência a ela usando o nome do arquivo (por exemplo, `#r "MyAssembly.dll"`). Para obter informações sobre como carregar arquivos na pasta da função, consulte a seção a seguir sobre gerenciamento de pacotes.

## Gerenciamento de pacote

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

Quando você carrega um arquivo *project.json*, o tempo de execução obtém os pacotes e adiciona referências automaticamente aos assemblies do pacote. Você não precisa adicionar diretivas `#r "AssemblyName"`. Basta adicionar as instruções `using` obrigatórias ao arquivo *run.csx* para usar os tipos definidos nos pacotes NuGet.


### Como carregar um arquivo project.json

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

## Variáveis de ambiente

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

## Reutilização de código .csx

Você pode usar classes e métodos definidos em outros arquivos *.csx* no seu arquivo *run.csx*. Para fazer isso, use as diretivas `#load` no seu arquivo *run.csx*, conforme mostrado no exemplo a seguir.

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

Você pode usar um caminho relativo com a diretiva `#load`:

* `#load "mylogger.csx"` carrega um arquivo localizado na pasta de função.

* `#load "loadedfiles\mylogger.csx"` carrega um arquivo localizado em uma pasta na pasta de função.

* `#load "..\shared\mylogger.csx"` carrega um arquivo localizado em uma pasta no mesmo nível que a pasta de função, ou seja, diretamente em *wwwroot*.
 
A diretiva `#load` só funciona com arquivos *.csx* (script C#), e não com arquivos *.cs*.

## Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Referência do desenvolvedor de NodeJS do Azure Functions](functions-reference-fsharp.md)
* [Referência do desenvolvedor de NodeJS do Azure Functions](functions-reference-node.md)
* [Gatilhos e bindings do Azure Functions](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0921_2016-->