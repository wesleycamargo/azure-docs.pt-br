<properties
	pageTitle="Referência do desenvolvedor do Azure Functions | Microsoft Azure"
	description="Entenda como desenvolver no Azure Functions usando NodeJS."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"/>

<tags
	ms.service="functions"
	ms.devlang="dotnet"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/14/2016"
	ms.author="chrande"/>

# Referência do desenvolvedor de C# do Azure Functions

A experiência do C# para Azure Functions baseia-se no SDK do Azure WebJobs. Fluxos de dados na sua função C# por meio de argumentos de método. Nomes de argumentos são especificados em `function.json` e há nomes predefinidos para acessar itens como agente de função e tokens de cancelamento.

Este artigo pressupõe que você já tenha lido a [referência do desenvolvedor do Azure Functions](functions-reference.md).

## Como o .csx funciona

O formato `.csx` permite escrever menos "clichê" e se concentrar em escrever apenas uma função C#. Para o Azure Functions, inclua as referências de assembly e namespaces que você precisa na parte superior, como de costume, e em vez de encapsular tudo em um namespace e classe, você pode apenas definir seu método `Run`. Se você precisar incluir alguma classe, por exemplo, para definir objetos POCO, você poderá incluir uma classe dentro do mesmo arquivo.

## Associando a argumentos

Várias associações estão associadas a uma função C# por meio da propriedade `name` na configuração *function.json*. Cada associação possui seus próprios tipos com suporte, que estão documentados por associação; por exemplo, um gatilho de blob pode dar suporte a cadeia de caracteres, POCO e vários outros tipos. Você pode usar o tipo que melhor atenda às suas necessidades.

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
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
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

Em alguns casos, você pode ter operações que são sensíveis ao desligamento. Embora sempre seja melhor escrever código que pode lidar com falhas, em casos em que você deseja lidar com solicitações de desligamento normal, defina uma argumento tipado [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx). Um `CancellationToken` será fornecido se o desligamento do host for disparado.

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

Se você precisar importar namespaces, poderá fazer como geralmente faz, com a cláusula `using`.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Os seguintes namespaces são automaticamente importados e, portanto, são opcionais:

* `System`
* `System.Collections.Generic`
* `System.Linq`
* `System.Net.Http`
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

Além disso, os seguintes assemblies têm regras de maiúsculas e minúsculas especias e podem ser referenciados por simplename (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Se você precisar fazer referência a um assembly particular, poderá carregar o arquivo do assembly para uma pasta `bin` referente à sua função e fazer referência a ela usando o nome do arquivo (por exemplo, `#r "MyAssembly.dll"`).

## Gerenciamento de pacote

Para usar os pacotes do NuGet em uma função C#, carregue um arquivo *project.json* para a pasta da função no sistema de arquivos do aplicativo da função. Aqui está um arquivo *project.json* de exemplo que adiciona uma referência à versão 1.1.0 do Microsoft.ProjectOxford.Face:

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

Quando você carregar um arquivo *project.json*, o tempo de execução obtém os pacotes e adiciona automaticamente referências aos assemblies do pacote. Você não precisa adicionar diretivas `#r "AssemblyName"`. Basta adicionar as instruções `using` obrigatórias ao arquivo *run.csx* para usar tipos definidos nos pacotes NuGet.

### Como carregar um arquivo project.json

Comece verificando se o aplicativo está em execução, o que pode ser feito abrindo a função no portal do Azure. Isso também permite acessar os logs de streaming nos quais a saída da instalação do pacote será exibida.

Os aplicativos de função baseiam-se no Serviço de Aplicativo, portanto, todas as [opções de implantação disponíveis para aplicativos Web padrão](../app-service-web/web-sites-deploy.md) também estão disponíveis para aplicativos de função. Aqui estão alguns métodos que você pode usar.

#### Para carregar o project.json usando o Visual Studio Online (Monaco)

1. No portal do Azure Functions, clique em **Configurações do aplicativo de função**.

2. Na seção **Configurações Avançadas**, clique em **Ir para Configurações do Serviço de Aplicativo**.

3. Clique em **Ferramentas**.

4. Em **Desenvolver**, clique em **Visual Studio Online**.

5. **Ative-o** se ainda não estiver habilitado e clique em **Ir**.

6. Após o Visual Studio Online ser carregado, arraste e solte seu arquivo *project.json* para a pasta da função (a pasta com o mesmo nome da sua função).

#### Para carregar o project.json usando o ponto de extremidade SCM (Kudu) do aplicativo de função

1. Navegue para: *https://<function_app_name>.scm.azurewebsites.net*.

2. Clique em **Console de Depuração > CMD**.

3. Navegue para *D:\\home\\site\\wwwroot<nome\_da\_função>*.

4. Arraste e solte seu arquivo *project.json* na pasta (para a grade de arquivos).

#### Carregar o project.json usando FTP

1. Siga as instruções [aqui](../app-service-web/web-sites-deploy.md#ftp) para configurar o FTP.

2. Quando você estiver conectado ao site do aplicativo de função, copie o arquivo *project.json* para */site/wwwroot/<function_name>*.

#### Log de instalação do pacote 

Após o arquivo *project.json* ser carregado, você verá a saída como o exemplo a seguir no log de streaming da sua função:

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

## Reutilização de código .csx

Você pode usar classes e métodos definidos em outros arquivos *.csx* no seu arquivo *run.csx*. Para fazer isso, use as diretivas `#load` no seu arquivo *run.csx*, conforme mostrado no exemplo a seguir.

*run.csx* de exemplo:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemplo *mylogger.csx*:

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
 
A diretiva `#load` só funciona com arquivos *.csx* (scritp C#), não com arquivos *.cs* arquivos.

## Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Referência do desenvolvedor de NodeJS do Azure Functions](functions-reference-node.md)
* [Gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0420_2016-->