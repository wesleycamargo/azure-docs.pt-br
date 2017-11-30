---
title: "Associações dos Hubs de Eventos do Azure Functions"
description: "Entenda como usar associações dos Hubs de Eventos do Azure no Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: wesmc
ms.openlocfilehash: c2660a3ca8ee7569d49a6998d0dfd5a98a97d294
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="azure-functions-event-hubs-bindings"></a>Associações dos Hubs de Eventos do Azure Functions

Este artigo explica como trabalhar com associações de [Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) para o Azure Functions. O Azure Functions dá suporte a associações de gatilho e de saída para os Hubs de Eventos.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="event-hubs-trigger"></a>Gatilho dos Hubs de Eventos

Use o gatilho dos Hubs de Eventos do Azure para responder a um evento enviado para uma transmissão de evento do hub de eventos. É necessário ter acesso de leitura ao hub de eventos para configurar o gatilho.

Quando uma função de gatilho dos Hubs de Eventos é disparada, a mensagem que a dispara é passada para a função como cadeia de caracteres.

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [Pré-compilado C#](#trigger---c-example)
* [Script C#](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Gatilho - exemplo C#

O exemplo a seguir mostra um código [C# pré-compilado](functions-dotnet-class-library.md) que registra o corpo da mensagem do gatilho dos hubs de eventos.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Para obter acesso aos metadados do evento, faça a associação a um objeto de [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer uma instrução `using` para `Microsoft.ServiceBus.Messaging`).

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```
Para receber eventos em um lote, faça `string` ou `EventData` uma matriz:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Gatilho - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função registra o corpo da mensagem do gatilho de hub de eventos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```
Aqui está o código de script do C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Para obter acesso aos metadados do evento, associe um objeto de [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer o uso de uma instrução para `Microsoft.ServiceBus.Messaging`).

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Para receber eventos em um lote, faça `string` ou `EventData` uma matriz:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Gatilho - exemplo F#

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função F#](functions-reference-fsharp.md) que usa a associação. A função registra o corpo da mensagem do gatilho de hub de eventos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

O código F# é o seguinte:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Gatilho - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função registra o corpo da mensagem do gatilho de hub de eventos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

## <a name="trigger---attributes-for-precompiled-c"></a>Gatilho - Atributos para pré-compilado C#

Para funções [C# pré-compiladas](functions-dotnet-class-library.md), use o atributo [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs), definido no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

O construtor do atributo usa os nomes do hub de eventos, do grupo de consumidores e de uma configuração de aplicativo que contenham a cadeia de caracteres de conexão. Para obter mais informações sobre essas configurações, consulte a [seção sobre configuração do gatilho](#trigger---configuration). Este é um `EventHubTriggerAttribute` exemplo de atributo:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
```

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `EventHubTrigger` atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `eventHubTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa o item de evento no código de função. | 
|**path** |**EventHubName** | O nome do hub de eventos. | 
|**consumerGroup** |**ConsumerGroup** | É uma propriedade opcional que define o [grupo de consumidores ](../event-hubs/event-hubs-features.md#event-consumers) usado para assinar eventos no hub. Se omitido, o grupo de consumidores `$Default` será usado. | 
|**conexão** |**Conexão** | É o nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie essa cadeia de conexão clicando no botão **Informações de Conexão** do *namespace*, não no próprio hub de eventos. Essa cadeia de conexão deve ter, pelo menos, permissões de leitura para ativar o gatilho.<br/>Quando você estiver desenvolvendo localmente, as configurações de aplicativo entram em valores do [arquivo local.settings.json](functions-run-local.md#local-settings-file).|

## <a name="trigger---hostjson-properties"></a>Gatilho - propriedades de host.json

O arquivo [host.json](functions-host-json.md#eventhub) contém configurações que controlam o comportamento de gatilho dos Hubs de Eventos.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="event-hubs-output-binding"></a>Associação de saída dos Hubs de Eventos

Use a associação de saída dos Hubs de Eventos para gravar eventos em um fluxo de eventos. É necessário ter permissão de envio para um hub de eventos a fim de gravar eventos nele.

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico a um idioma:

* [Pré-compilado C#](#output---c-example)
* [Script C#](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo C#

O exemplo a seguir mostra uma [função C# pré-compilada](functions-dotnet-class-library.md) que grava uma mensagem em um hub de eventos, usando o valor de retorno do método como a saída:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Este é o código C# script que cria uma mensagem:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Este é o código C# script que cria várias mensagens:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Saída - Exemplo #F

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função F#](functions-reference-fsharp.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

O código F# é o seguinte:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Saída - exemplo JavaScript

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Este é o código JavaScript que envia uma mensagem única:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Este é o código JavaScript que envia várias mensagens:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes-for-precompiled-c"></a>Saída - Atributos para pré-compilado C#

Para funções [C# pré-compiladas](functions-dotnet-class-library.md), use o atributo [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), definido no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

O construtor do atributo usa os nomes do hub de eventos e de uma configuração de aplicativo que contenham a cadeia de caracteres de conexão. Para obter mais informações sobre essas configurações, consulte [Saída - configuração](#output---configuration). Este é um `EventHub` exemplo de atributo:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
```

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `EventHub` atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "eventHub". |
|**direction** | n/d | Deve ser definido como "out". Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | É o nome da variável usada no código da função que representa o evento. | 
|**path** |**EventHubName** | O nome do hub de eventos. | 
|**conexão** |**Conexão** | É o nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie essa cadeia de conexão clicando no botão **Informações de Conexão** do *namespace*, não no próprio hub de eventos. Essa cadeia de conexão deve ter permissões de envio para enviar a mensagem à transmissão do evento.<br/>Quando você estiver desenvolvendo localmente, as configurações de aplicativo entram em valores do [arquivo local.settings.json](functions-run-local.md#local-settings-file).|

## <a name="output---usage"></a>Saída - uso

Em C# e C# script, envie mensagens usando um parâmetro de método, como `out string paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Para gravar várias mensagens, você pode usar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

Em JavaScript, acesse o evento de saída usando `context.bindings.<name>`. `<name>` É o valor especificado na propriedade `name` de *function.json*.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
