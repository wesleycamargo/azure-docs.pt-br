---
title: Referência do desenvolvedor de C# do Azure Functions
description: Entenda como desenvolver no Azure Functions usando NodeJS.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 9f538b48918bdde923c6dbf3999302e45b955035
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092403"
---
# <a name="azure-functions-c-developer-reference"></a>Referência do desenvolvedor de C# do Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Este artigo é uma introdução ao desenvolvimento do Azure Functions usando o script C# em biblioteca de classes .NET.

O Azure Functions oferece suporte às linguagens de programação C# e script C#. Se estiver procurando diretrizes sobre [como usar C# no portal do Azure](functions-create-function-app-portal.md), consulte [Referência do desenvolvedor de script C# (.csx)](functions-reference-csharp.md).

Este artigo pressupõe que você tenha completado as seguintes etapas:

* [Guia do desenvolvedor do Azure Functions](functions-reference.md)
* [Ferramentas do Visual Studio 2017 para Azure Functions](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Projeto de biblioteca de classes de funções

No Visual Studio, o modelo de projeto do **Azure Functions** cria um projeto de biblioteca de classes do C# que contém os seguintes arquivos:

* [host.json](functions-host-json.md) – armazena conjuntos de configurações que afetam todas as funções no projeto quando estão em execução localmente ou no Azure.
* [local.settings.json](functions-run-local.md#local-settings-file) – armazena as configurações de aplicativo e as cadeias de conexão que são usadas quando estão em execução localmente.

> [!IMPORTANT]
> O processo de compilação cria um arquivo *function.json* para cada função. Esse arquivo *function.json* não deve ser editado diretamente. Você não pode alterar a configuração de associação ou desabilitar a função por meio da edição desse arquivo. Para desabilitar uma função, use o atributo [Desabilitar](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs). Por exemplo, adicione uma configuração de aplicativo booliana MY_TIMER_DISABLED e aplique `[Disable("MY_TIMER_DISABLED")]` para a função. É possível habilitar e desabilitar a função, alterando a configuração de aplicativo.

## <a name="methods-recognized-as-functions"></a>Métodos reconhecidos como funções

Em uma biblioteca de classe, uma função é um método estático com um `FunctionName` e um atributo de gatilho, conforme mostrado no exemplo a seguir:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

O atributo `FunctionName` marca o método como um ponto de entrada da função. O nome deve ser exclusivo dentro de um projeto, começar com uma letra e conter apenas letras, números, `_` e `-`, até 127 caracteres. Modelos de projeto geralmente criam um método chamado `Run`, mas o nome do método pode ser qualquer nome de método C# válido.

O atributo de gatilho especifica o tipo de gatilho e associa dados de entrada a um parâmetro de método. A função de exemplo é disparada por uma mensagem de fila, a qual é transmitida para o método no parâmetro `myQueueItem`.

## <a name="method-signature-parameters"></a>Parâmetros de assinatura do método

A assinatura do método pode conter parâmetros diferentes daquela usada com o atributo de gatilho. Aqui estão alguns dos parâmetros adicionais que você pode incluir:

* [Associações de entrada e saída](functions-triggers-bindings.md) marcadas como tal, decorando-os com atributos.  
* Um parâmetro `ILogger` ou `TraceWriter` para [efetuar logon](#logging).
* Um parâmetro `CancellationToken` para [desligamento normal](#cancellation-tokens).
* Parâmetros de [expressões de associação](functions-triggers-bindings.md#binding-expressions-and-patterns) para obter metadados de gatilho.

Não importa a ordem dos parâmetros na assinatura de função. Por exemplo, você pode inserir os parâmetros de gatilho antes ou depois de outras associações e inserir o parâmetro do agente antes ou depois dos parâmetros de gatilho ou associação.

### <a name="output-binding-example"></a>Exemplo de associação de saída

O exemplo a seguir modifica o anterior por adicionar uma associação de fila de saída. A função grava a mensagem da fila que aciona a função para uma nova mensagem de fila em uma fila diferente.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Os artigos de referência de associação ([Filas de armazenamento](functions-bindings-storage-queue.md), por exemplo) explicam quais tipos de parâmetro você pode usar com os atributos de associação de gatilho, entrada ou saída.

### <a name="binding-expressions-example"></a>Exemplo de expressões de associação

O código a seguir obtém o nome da fila para monitorar a partir de uma configuração de aplicativo, e ele obtém a hora de criação da mensagem da fila no parâmetro `insertionTime`.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        TraceWriter log)
    {
        log.Info($"Message content: {myQueueItem}");
        log.Info($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>function.json gerado automaticamente

O processo de compilação cria um arquivo *function.json* em uma pasta de função na pasta de compilação. Conforme observado anteriormente, esse arquivo não deve ser editado diretamente. Você não pode alterar a configuração de associação ou desabilitar a função por meio da edição desse arquivo. 

O objetivo desse arquivo é fornecer informações para o controlador de escala usado para [dimensionar decisões no plano de consumo](functions-scale.md#how-the-consumption-plan-works). Por esse motivo, o arquivo não tem informações de associações de entrada ou saída, apenas de gatilho.

O arquivo *function.json* gerado inclui uma propriedade `configurationSource` que indica o tempo de execução a ser usado em atributos .NET para associações, em vez da configuração do *function.json*. Aqui está um exemplo:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

A geração do arquivo *function.json* é realizada pelo pacote NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

O mesmo pacote é usado para a versão 1.x e 2.x do tempo de execução do Functions. A estrutura de destino é o que diferencia um projeto de 1.x de um projeto de 2.x. Estas são as partes relevantes dos arquivos *.csproj*, mostrando estruturas de destino diferentes e o mesmo pacote `Sdk`:

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netstandard2.0</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Entre as dependências do pacote `Sdk` estão os gatilhos e associações. Um projeto do 1.x se refere a gatilhos e associações do 1.x, pois são direcionados ao .NET Framework, enquanto os gatilhos e associações do 2.x são direcionados ao .NET Core.

O pacote `Sdk` também depende do [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) e, indiretamente, do [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage). Essas dependências garantem que seu projeto use as versões desses pacotes que funcionam com a versão de tempo de execução do Functions para a qual o projeto é direcionado. Por exemplo, o `Newtonsoft.Json` tem a versão 11 para o .NET Framework 4.6.1, mas o tempo de execução do Functions direcionado para o .NET Framework 4.6.1 só é compatível com o `Newtonsoft.Json` 9.0.1. Portanto, o código de sua função nesse projeto também tem que usar `Newtonsoft.Json` 9.0.1.

O código-fonte para `Microsoft.NET.Sdk.Functions`está disponível no repositório GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Versão de tempo de execução

O Visual Studio usa as [Ferramentas Essenciais do Azure Functions](functions-run-local.md#install-the-azure-functions-core-tools) para executar projetos do Functions. As Ferramentas Essenciais são uma interface de linha de comando para o tempo de execução do Functions.

Se você instalar as Ferramentas Essenciais usando npm, isso não afetará a versão das Ferramentas Essenciais usada pelo Visual Studio. Para a versão de tempo de execução do Functions 1.x, o Visual Studio armazena as versões das Ferramentas Essenciais em *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* e usa a versão mais recente armazenada ali. Para o Functions 2.x, as Ferramentas Essenciais serão incluídas na extensão **Azure Functions e Ferramentas de Trabalhos Web**. Para 1.x e 2.x, você pode ver qual versão está sendo usado na saída do console ao executar um projeto do Functions:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Tipos com suporte para associações

Cada associação tem seus próprios tipos com suporte. Por exemplo, um atributo de gatilho de blob pode ser aplicado a um parâmetro de cadeia de caracteres, um parâmetro POCO, um parâmetro `CloudBlockBlob` ou qualquer um dos vários outros tipos com suporte. O [artigo de referência de associação para associações de blob](functions-bindings-storage-blob.md#trigger---usage) lista todos os tipos de parâmetro com suporte. Para obter mais informações, consulte [Gatilhos e associações](functions-triggers-bindings.md) e os [documentos de referência de associação para cada tipo de associação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Associando ao valor de retorno do método

Você pode usar um valor de retorno do método para uma associação de saída, aplicando o atributo ao valor de retorno do método. Para obter exemplos, consulte [Gatilhos e associações](functions-triggers-bindings.md#using-the-function-return-value). 

Use o valor retornado apenas se uma execução de função com êxito sempre resultar em um valor retornado a ser passado para a associação de saída. Caso contrário, use `ICollector` ou `IAsyncCollector`, conforme mostrado na seção a seguir.

## <a name="writing-multiple-output-values"></a>Gravando vários valores de saída

Para gravar vários valores em uma associação de saída ou se uma invocação de função com êxito não resultar em nada a ser passado para a associação de saída, use os tipos [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Esses tipos são coleções somente gravação que são gravadas na associação de saída quando o método é concluído.

Este exemplo grava várias mensagens de fila na mesma fila usando `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Registro em log

Para registrar a saída nos logs de streaming em C#, inclua um argumento do tipo `TraceWriter`. Recomendamos nomeá-lo como `log`. Evite usar `Console.Write` no Azure Functions. 

`TraceWriter` é definido no [SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). O nível de log para `TraceWriter` pode ser configurado em [host.json](functions-host-json.md).

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> Para obter informações sobre uma estrutura de log mais recente que pode ser usada no lugar de `TraceWriter`, consulte [Gravar logs nas funções C#](functions-monitoring.md#write-logs-in-c-functions) no artigo **Monitorar o Azure Functions**.

## <a name="async"></a>Assíncrono

Para tornar uma função [assíncrona](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), use a palavra-chave `async` e retorne um objeto `Task`.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Não é possível usar parâmetros `out` em funções assíncronas. Para associações de saída, use o [valor de retorno de função](#binding-to-method-return-value) ou um [objeto coletor](#writing-multiple-output-values).

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Uma função pode aceitar um parâmetro [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) que permite ao sistema operacional notificar seu código quando a função está prestes a ser encerrada. Você pode usar essa notificação para certificar-se de que a função não finalize inesperadamente de uma maneira que os dados fiquem em um estado inconsistente.

O exemplo a seguir mostra como verificar o encerramento da função iminente.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
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
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>Variáveis de ambiente

Para obter uma variável de ambiente ou um valor de configuração do aplicativo, use `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo de código a seguir:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
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
}
```

As configurações do aplicativo podem ser lidas de variáveis de ambiente ao desenvolver localmente e ao executar no Azure. Ao desenvolver localmente, as configurações do aplicativo são provenientes da coleção `Values` no arquivo *local.settings.json*. Em ambos os ambientes, local e do Azure, `GetEnvironmentVariable("<app setting name>")` recupera o valor da configuração de aplicativo nomeada. Por exemplo, quando você estivesse executando localmente, "Nome do Meu Site" seria retornado se o arquivo *local.settings.json* contivesse `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

A propriedade [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) é uma API alternativa para obter os valores de configuração do aplicativo, mas é recomendável que você use `GetEnvironmentVariable` conforme mostrado aqui.

## <a name="binding-at-runtime"></a>Associando no tempo de execução

No C# e em outras linguagens .NET, é possível usar um padrão de associação [obrigatório](https://en.wikipedia.org/wiki/Imperative_programming) em vez de associações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em atributos. A associação obrigatória é útil quando os parâmetros de associação precisam ser calculado no tempo de execução, em vez do tempo de design. Com esse padrão, é possível se vincular a associações de entrada e saída com suporte instantaneamente no código da função.

Defina uma associação obrigatória da seguinte maneira:

- **Não** inclua um atributo em na assinatura de função para as associações obrigatórias desejadas.
- Passe um parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Use o padrão de C# a seguir para realizar a associação de dados.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` é o atributo do .NET que define a associação, e `T` é um tipo de entrada ou saída com suporte nesse tipo de associação. `T` não pode ser um tipo de parâmetro `out` (como `out JObject`). Por exemplo, a associação de saída de tabela dos Aplicativos Móveis dá suporte a [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas você só pode usar [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) com a associação imperativa.

### <a name="single-attribute-example"></a>Exemplo de atributo único

O código de exemplo a seguir cria uma [associação de saída do Armazenamento de Blobs](functions-bindings-storage-blob.md#output) com o caminho do blob definido em tempo de execução e grava uma cadeia de caracteres no blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define a associação de entrada ou saída do [Armazenamento de Blobs](functions-bindings-storage-blob.md) e [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) é um tipo de associação de saída com suporte.

### <a name="multiple-attribute-example"></a>Exemplo de atributo múltiplo

O exemplo anterior obtém a configuração do aplicativo para a cadeia de conexão da conta de armazenamento principal do aplicativo de funções (que é `AzureWebJobsStorage`). É possível especificar uma configuração de aplicativo personalizada a ser usada para a conta de armazenamento adicionando [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando a matriz de atributos para `BindAsync<T>()`. Use um parâmetro `Binder`, não `IBinder`.  Por exemplo: 

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Gatilhos e associações 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre gatilhos e associações](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre práticas recomendadas do Azure Functions](functions-best-practices.md)
