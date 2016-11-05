---
title: Gatilhos e associações do Barramento de Serviço do Azure Functions | Microsoft Docs
description: Entenda como usar gatilhos e associações do Barramento de Serviço do Azure no Azure Functions.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: ''
tags: ''
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2016
ms.author: chrande; glenga

---
# Gatilhos e associações do Barramento de Serviço do Azure Functions para filas e tópicos
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar gatilhos e associações do Barramento de Serviço do Azure no Azure Functions.

[!INCLUDE [introdução](../../includes/functions-bindings-intro.md)]

## <a id="sbtrigger"></a> Gatilho de fila ou de tópico do Barramento de Serviço
#### function.json
O arquivo *function.json* especifica as propriedades a seguir.

* `name`: o nome da variável usada no código de função para a fila ou tópico, ou a mensagens de tópico ou fila.
* `queueName`: para gatilho de fila somente, o nome da fila a ser sondada.
* `topicName`: para gatilho de tópico somente, o nome do tópico a ser sondado.
* `subscriptionName`: para gatilho de tópico somente, o nome da assinatura.
* `connection`: o nome de uma configuração de aplicativo que contém uma cadeia de conexão do Barramento de Serviço. A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico. Se a cadeia de conexão não gerenciar direitos, defina a propriedade `accessRights`. Se você deixar `connection` vazio, o gatilho ou a associação funcionará com a cadeia de conexão do Barramento de Serviço padrão para o aplicativo de funções, que é especificado pela configuração do aplicativo AzureWebJobsServiceBus.
* `accessRights`: especifica os direitos de acesso disponíveis para a cadeia de conexão. O valor padrão é `manage`. Defina como `listen` se você estiver usando uma cadeia de conexão que não fornece permissões de gerenciamento. Caso contrário, o tempo de execução do Functions pode tentar e falha ao executar operações que exigem gerenciamento de direitos.
* `type`: deve ser definido como *serviceBusTrigger*.
* `direction`: deve ser definido como *in*.

Exemplo do *function.json* para um gatilho de fila do Barramento de Serviço:

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Exemplo de código C# que processa uma mensagem de fila do Barramento de Serviço
```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Exemplo de código F# que processa uma mensagem de fila do Barramento de Serviço
```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### Exemplo de código Node.js que processa uma mensagem de fila do Barramento de Serviço
```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### Tipos com suporte
A mensagem da fila do Barramento de Serviço pode ser desserializada para qualquer um destes tipos:

* Objeto (de JSON)
* string
* matriz de bytes
* `BrokeredMessage` (C#)

#### <a id="sbpeeklock"></a> Comportamento de PeekLock
O tempo de execução do Functions receberá uma mensagem no modo `PeekLock` e chamadas `Complete` na mensagem se a função for concluída com êxito, ou chamadas `Abandon` se a função falhar. Se a função for executada por mais tempo que o limite `PeekLock`, o bloqueio é renovado automaticamente.

#### <a id="sbpoison"></a> Manipulação de mensagens suspeitas
O Barramento de Serviço faz seu próprio tratamento de mensagens suspeitas que não pode ser controlado ou definido na configuração ou código do Azure Functions.

#### <a id="sbsinglethread"></a> Threading único
Por padrão, o tempo de execução do Functions processa várias mensagens da fila simultaneamente. Para direcionar o tempo de execução para processar uma única fila ou mensagem de tópico de cada vez, defina `serviceBus.maxConcurrrentCalls` como 1 no arquivo *host.json*. Para obter informações sobre o arquivo *host.json*, consulte [Estrutura da Pastas](functions-reference.md#folder-structure) no artigo de referência do Desenvolvedor e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) no wiki do repositório WebJobs.Script.

## <a id="sboutput"></a> Associação de saída de fila ou de tópico do Barramento de Serviço do Azure
#### function.json
O arquivo *function.json* especifica as propriedades a seguir.

* `name`: o nome da variável usada no código de função para a fila ou mensagem da fila.
* `queueName`: para gatilho de fila somente, o nome da fila a ser sondada.
* `topicName`: para gatilho de tópico somente, o nome do tópico a ser sondado.
* `subscriptionName`: para gatilho de tópico somente, o nome da assinatura.
* `connection`: mesmo para o gatilho do Barramento de Serviço.
* `accessRights`: especifica os direitos de acesso disponíveis para a cadeia de conexão. O valor padrão é `manage`. Defina como `send` se você estiver usando uma cadeia de conexão que não fornece permissões de gerenciamento. Caso contrário, o tempo de execução do Functions pode tentar e falha ao executar operações que exigem gerenciamento de direitos, tais como da criação de filas.
* `type`: deve ser definido como *serviceBus*.
* `direction`: deve ser definido como *out*.

*function.json* de exemplo para usar um gatilho de temporizador para gravar mensagens de fila do Barramento de Serviço:

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Tipos com suporte
O Azure Functions pode criar uma mensagem de fila do Barramento de Serviço de qualquer um dos tipos a seguir.

* Objeto (sempre cria uma mensagem JSON, criará a mensagem com um objeto nulo se o valor for nulo quando a função terminar)
* cadeia de caracteres (criará uma mensagem se o valor não for nulo quando a função terminar)
* matriz de bytes (funciona como uma cadeia de caracteres)
* `BrokeredMessage` (C#, funciona como uma cadeia de caracteres)

Para criar várias mensagens em uma função C#, você pode usar `ICollector<T>` ou `IAsyncCollector<T>`. Uma mensagem é criada quando você chama o método `Add`.

#### Exemplos de código C# que cria mensagens de fila do Barramento de Serviço
```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Exemplo de código F# que cria uma mensagem de fila do Barramento de Serviço
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### Exemplo de código Node.js que cria uma mensagem de fila do Barramento de Serviço
```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Próximas etapas
[!INCLUDE [próximas etapas](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->