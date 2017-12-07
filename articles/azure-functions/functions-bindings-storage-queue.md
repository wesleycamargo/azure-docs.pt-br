---
title: "Associações de armazenamento de filas do Azure Functions"
description: "Entenda como usar o gatilho do armazenamento de fila do Azure e a associação de saída no Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: glenga
ms.openlocfilehash: 0aae58fa52f9f7f64b08e1701b7688a90c56e6ed
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Associações de armazenamento de filas do Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de Fila do Azure no Azure Functions. O Azure Functions dá suporte a associações de gatilho e de saída para filas.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Gatilho

Use o gatilho de fila para iniciar uma função quando um novo item é recebido em uma fila. A mensagem da fila é fornecida como entrada para a função.

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [Pré-compilado C#](#trigger---c-example)
* [Script C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Gatilho - exemplo C#

O exemplo a seguir mostra um código [pré-compilado C#](functions-dotnet-class-library.md) que controla a `myqueue-items` fila e grava um log cada vez que um item de fila é processado.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Gatilho - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho de blob em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função controla a `myqueue-items` fila e grava um log cada vez que um item de fila é processado.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

A seção [uso](#trigger---usage) explica `myQueueItem`, que é chamado pela `name` propriedade function.json.  A [seção de metadados de mensagem](#trigger---message-metadata) explica todas as outras variáveis mostradas.

### <a name="trigger---javascript-example"></a>Gatilho - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de blob em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função controla a `myqueue-items` fila e grava um log cada vez que um item de fila é processado.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A seção [configuração](#trigger---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

A seção [uso](#trigger---usage) explica `myQueueItem`, que é chamado pela `name` propriedade function.json.  A [seção de metadados de mensagem](#trigger---message-metadata) explica todas as outras variáveis mostradas.

## <a name="trigger---attributes"></a>Gatilho - atributos
 
Para funções [pré-compilado C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de fila:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs) é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  O construtor do atributo usa o nome da fila para monitorar, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  {
      ...
  }
  ```

  Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  {
      ....
  }
  ```
 
  Para ver um exemplo completo, consulte [Gatilho – exemplo de C# pré-compilado](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Oferece uma maneira de especificar a conta de armazenamento para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* A propriedade `QueueTrigger` do atributo`Connection`.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro do `QueueTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A configuração do aplicativo "AzureWebJobsStorage".

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `QueueTrigger` atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d| Deve ser definido como `queueTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction**| n/d | Apenas no arquivo *function.json*. Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d |O nome da variável que representa a fila no código de função.  | 
|**queueName** | **QueueName**| O nome da fila a ser controlada. | 
|**conexão** | **Conexão** |O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho - uso
 
Em C# e script C#, acesse os dados de blob usando um parâmetro de método, como `Stream paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. É possível associar a qualquer um dos seguintes tipos:

* Objeto POCO - O tempo de execução do Functions desserializa um conteúdo JSON no objeto POCO. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Em JavaScript, use `context.bindings.<name>` para acessar o conteúdo de item de fila. Se o conteúdo for JSON, ele é desserializado em um objeto.

## <a name="trigger---message-metadata"></a>Gatilho - metadados da mensagem

O gatilho de fila fornece várias propriedades de metadados. Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Os valores têm a mesma semântica que [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage).

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`QueueTrigger`|`string`|Conteúdo da fila (se for uma cadeia de caracteres válida). Se o conteúdo de mensagem de fila como uma cadeia de caracteres `QueueTrigger` tem o mesmo valor da variável nomeada pela `name` propriedade em *function.json*.|
|`DequeueCount`|`int`|O número de vezes que essa mensagem foi removida da fila.|
|`ExpirationTime`|`DateTimeOffset?`|A hora em que a mensagem expira.|
|`Id`|`string`|ID da mensagem da fila.|
|`InsertionTime`|`DateTimeOffset?`|A hora em que a mensagem foi adicionada à fila.|
|`NextVisibleTime`|`DateTimeOffset?`|A hora em que a mensagem estará visível.|
|`PopReceipt`|`string`|Recebimento pop da mensagem.|

## <a name="trigger---poison-messages"></a>Gatilho - mensagens suspeitas

Quando uma função do gatilho de fila falhar, o Azure Functions repetirá essa função até cinco vezes para uma determinada mensagem da fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, o tempo de execução das funções adicionará uma mensagem em uma fila chamada *&lt;originalqueuename>-poison*. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária.

Para tratar mensagens suspeitas manualmente, verifique o [dequeueCount](#trigger---message-metadata) da mensagem de fila.

## <a name="trigger---hostjson-properties"></a>Gatilho - propriedades de host.json

O arquivo [host.json](functions-host-json.md#queues) contém configurações que controlam o comportamento de gatilho de fila.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Saída

Use a associação de saída do armazenamento de Filas do Azure para que você grave mensagens em uma fila.

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico a um idioma:

* [Pré-compilado C#](#output---c-example)
* [Script C#](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo C#

O exemplo a seguir mostra um código [pré-compilado C#](functions-dotnet-class-library.md) que cria uma mensagem da fila para cada solicitação HTTP recebida.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho de blob em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função cria um item da fila com uma carga POCO para cada solicitação HTTP recebida.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código script C# que cria uma mensagem de fila única:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Você pode enviar várias mensagens ao mesmo tempo usando um parâmetro `ICollector` ou `IAsyncCollector`. Aqui está o código de script C# que envia várias mensagens, uma com os dados da solicitação HTTP e outra com valores codificados:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItem, 
    TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Saída - exemplo JavaScript

O exemplo a seguir mostra uma associação de gatilho de blob em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função cria um item da fila para cada solicitação HTTP recebida.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Você pode enviar várias mensagens de uma vez com a definição de uma matriz de mensagem para a `myQueueItem` associação de saída. O código JavaScript a seguir envia duas mensagens de fila com valores codificados para cada solicitação HTTP recebida.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes"></a>Saída - atributos
 
Para funções [pré-compiladas C#](functions-dotnet-class-library.md), use o [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), que é definido no pacote NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

O atributo se aplica a um `out` parâmetro ou o valor de retorno da função. O construtor do atributo usa o nome da fila, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items, Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Para ver um exemplo completo, consulte [Saída - exemplo de C# pré-compilado](#output---c-example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Gatilho - atributos](#trigger---attributes-for-precompiled-c).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `Queue` atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `queue`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `out`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila no código de função. Definido como `$return` para referenciar o valor de retorno da função.| 
|**queueName** |**QueueName** | O nome da fila. | 
|**conexão** | **Conexão** |O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - uso
 
Em C# e script C#, grave uma mensagem de fila única usando um parâmetro de método como o `out T paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Você pode usar o tipo de retorno de método em vez de um `out` parâmetro, e `T` pode ser qualquer um dos seguintes tipos:

* Um POCO serializado como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Em C# e script C#, grave várias mensagens de fila usando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

Em funções em JavaScript, use `context.bindings.<name>` para acessar a mensagem de fila de saída. Você pode usar uma cadeia de caracteres ou um objeto serializável em JSON para o conteúdo de item de fila.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Vá para um guia de início rápido que usa um gatilho de armazenamento de Fila](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Vá para um tutorial que usa uma associação de saída de armazenamento de Fila](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage