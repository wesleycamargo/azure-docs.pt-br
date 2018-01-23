---
title: "Associações de Hubs de Eventos do Azure para o Azure Functions"
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
ms.openlocfilehash: 0d48d0b008d76cfb2d7d7815a69774976e184467
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Associações de Hubs de Eventos do Azure para o Azure Functions

Este artigo explica como trabalhar com associações de [Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md) para o Azure Functions. O Azure Functions dá suporte a associações de gatilho e de saída para os Hubs de Eventos.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Gatilho

Use o gatilho dos Hubs de Eventos do Azure para responder a um evento enviado para uma transmissão de evento do hub de eventos. É necessário ter acesso de leitura ao hub de eventos para configurar o gatilho.

Quando uma função de gatilho dos Hubs de Eventos é disparada, a mensagem que a dispara é passada para a função como cadeia de caracteres.

## <a name="trigger---scaling"></a>Gatilho - dimensionamento

Cada instância de uma função disparada pelo Hub de Eventos é suportada por apenas uma instância de EPH (EventProcessorHost). Os Hubs de Eventos fazem com que apenas 1 EPH possa obter uma concessão em determinada partição.

Por exemplo, suponha que vamos começar com a configuração e as premissas para um Hub de Eventos abaixo:

1. 10 partições.
1. 1000 eventos distribuídos uniformemente em todas as partições => 100 mensagens em cada partição.

Quando sua função é habilitada pela primeira vez, há apenas uma instância da função. Vamos chamar essa instância de função Function_0. Function_0 terá 1 EPH que consegue obter uma concessão em todas as 10 partições. Ela começará a ler eventos das partições 0 a 9. Daqui em diante, uma destas opções ocorrerá:

* **Somente uma instância de função é necessária**-: Function_0 é capaz de processar todos os 1000 antes que lógica de dimensionamento do Azure Functions seja ativada. Portanto, todas as 1000 mensagens são processadas pela Function_0.

* **Adicionar mais uma instância de função**: a lógica de dimensionamento do Azure Functions determina que Function_0 tem mais mensagens do que ela pode processar e, portanto, uma nova instância, Function_1, é criada. Os Hubs de Eventos detectam que uma nova instância EPH está tentando ler mensagens. Os Hubs de Eventos iniciam o balanceamento de carga das partições entre as instâncias EPH, por exemplo,as partições de 0 a 4 são atribuídas a Function_0 e as partições de 5 a 9 são atribuídas a Function_1. 

* **Adicionar mais N instâncias de função**: a lógica de dimensionamento do Azure Functions determina que Function_0 e Function_1 têm mais mensagens do que podem processar. Ela escala novamente para Function_2...N, em que N é maior do que as partições do Hub de Eventos. Os Hubs de Eventos fazem o balanceamento de carga das partições nas instâncias Function_0 a Function_9.

O que é único na lógica de dimensionamento do Azure Functions é o fato de que N é maior que o número de partições. Isso é feito para garantir que sempre existam instâncias do EPH prontamente disponíveis para garantir rapidamente as partições quando ficam disponíveis de outras instâncias. Os usuários pagam apenas pelos recursos usados quando a instância de função é executada e não serão cobrados por esse excesso de provisionamento.

Se todas as execuções de função tiverem êxito sem erros, os pontos de verificação serão adicionados à conta de armazenamento associada. Quando a verificação é bem-sucedida, todas as 1000 mensagens deixam de ser recuperadas.

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#trigger---c-example)
* [Script do C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Gatilho - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que registra o corpo da mensagem do gatilho dos hub de eventos.

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

## <a name="trigger---attributes"></a>Gatilho – atributos

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use o atributo [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs), definido no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

O construtor do atributo usa os nomes do hub de eventos, do grupo de consumidores e de uma configuração de aplicativo que contenham a cadeia de caracteres de conexão. Para obter mais informações sobre essas configurações, consulte a [seção sobre configuração do gatilho](#trigger---configuration). Este é um `EventHubTriggerAttribute` exemplo de atributo:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Para ver um exemplo completo, consulte [Gatilho – exemplo de C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `EventHubTrigger`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `eventHubTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa o item de evento no código de função. | 
|**path** |**EventHubName** | O nome do hub de eventos. | 
|**consumerGroup** |**ConsumerGroup** | É uma propriedade opcional que define o [grupo de consumidores ](../event-hubs/event-hubs-features.md#event-consumers) usado para assinar eventos no hub. Se omitido, o grupo de consumidores `$Default` será usado. | 
|**conexão** |**Conexão** | É o nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie essa cadeia de conexão clicando no botão **Informações de Conexão** do *namespace*, não no próprio hub de eventos. Essa cadeia de conexão deve ter, pelo menos, permissões de leitura para ativar o gatilho.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---hostjson-properties"></a>Gatilho - propriedades de host.json

O arquivo [host.json](functions-host-json.md#eventhub) contém configurações que controlam o comportamento de gatilho dos Hubs de Eventos.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Saída

Use a associação de saída dos Hubs de Eventos para gravar eventos em um fluxo de eventos. É necessário ter permissão de envio para um hub de eventos a fim de gravar eventos nele.

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#output---c-example)
* [Script do C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que grava uma mensagem em um hub de eventos, usando o valor retornado do método como a saída:

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

Este é o código de script C# que cria várias mensagens:

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

## <a name="output---attributes"></a>Saída - atributos

Para [bibliotecas de classes C#](functions-dotnet-class-library.md), use o atributo [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), definido no pacote NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

O construtor do atributo usa os nomes do hub de eventos e de uma configuração de aplicativo que contenham a cadeia de caracteres de conexão. Para obter mais informações sobre essas configurações, consulte [Saída - configuração](#output---configuration). Este é um `EventHub` exemplo de atributo:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#output---c-example).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `EventHub` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "eventHub". |
|**direction** | n/d | Deve ser definido como "out". Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | É o nome da variável usada no código da função que representa o evento. | 
|**path** |**EventHubName** | O nome do hub de eventos. | 
|**conexão** |**Conexão** | É o nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie essa cadeia de conexão clicando no botão **Informações de Conexão** do *namespace*, não no próprio hub de eventos. Essa cadeia de conexão deve ter permissões de envio para enviar a mensagem à transmissão do evento.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - uso

Em C# e C# script, envie mensagens usando um parâmetro de método, como `out string paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Para gravar várias mensagens, você pode usar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

Em JavaScript, acesse o evento de saída usando `context.bindings.<name>`. `<name>` é o valor especificado na propriedade `name` de *function.json*.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
