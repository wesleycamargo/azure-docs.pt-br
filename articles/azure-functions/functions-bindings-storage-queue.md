---
title: "Associações da fila do Armazenamento do Azure Functions | Microsoft Docs"
description: "Entenda como usar gatilhos e associações do Armazenamento do Azure em Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/18/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 770cac8809ab9f3d6261140333ec789ee1390daf
ms.openlocfilehash: bf9bd2a1b5acdf5a4a4f862bef693f8c60c63a33


---
# <a name="azure-functions-storage-queue-bindings"></a>Associações de fila do Armazenamento do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Esse artigo explica como configurar e codificar associações de fila do Armazenamento do Azure no Azure Functions. O Azure Functions dá suporte para associações de gatilho e de saída para as filas do Armazenamento do Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="storage-queue-trigger"></a>Gatilho da fila do Armazenamento
O gatilho da fila do Armazenamento do Azure permite que você monitore uma fila de armazenamento para novas mensagens e responda a elas. 

O gatilho da fila do Armazenamento para uma função usa os seguintes objetos JSON na matriz `bindings` do function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>",
    "type": "queueTrigger",
    "direction": "in"
}
```

`connection` deve conter o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. No portal do Azure, você pode definir essa configuração de aplicativo no **integrar** guia quando você cria uma conta de armazenamento ou selecione um existente. Para criar manualmente essa configuração de aplicativo, veja [Gerenciar as configurações do Serviço de Aplicativo](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings).

[Configurações adicionais](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) podem ser fornecidas em um arquivo host.json para ajustar ainda mais gatilhos da fila de armazenamento.  

### <a name="handling-poison-queue-messages"></a>Tratamento de mensagens suspeitas na fila
Quando uma função do gatilho de fila falhar, o Azure Functions repetirá essa função até cinco vezes para uma determinada mensagem da fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, o Functions adicionará uma mensagem em uma fila do Armazenamento chamada *&lt;originalqueuename>-poison*. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária. 

Para manipular mensagens suspeitas manualmente, você poderá obter o número de vezes que uma mensagem foi selecionada para processamento ao verificar `dequeueCount` (confira [Enfileirar metadados de gatilho](#meta)).

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso de gatilho
Nas funções do C#, você se vincula à mensagem de entrada usando um parâmetro nomeado na assinatura da função, como `<T> <name>`.
Onde `T` é o tipo de dados em que você deseja desserializar os dados e `paramName` é o nome especificado na [associação de gatilho](#trigger). Nas funções do Node.js, acesse os dados de blob de entrada usando `context.bindings.<name>`.

A mensagem da fila pode ser desserializada para qualquer um destes tipos:

* [Objeto](https://msdn.microsoft.com/library/system.object.aspx) - usado para mensagens serializadas para JSON. Quando você declarar um tipo personalizado de entrada, o tempo de execução tenta desserializar o objeto JSON. 
* Cadeia de caracteres
* Matriz de bytes
* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx) (somente C#)

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Metadados de gatilho de fila
Você pode obter metadados de fila em sua função usando estes nomes de variáveis:

* expirationTime
* insertionTime
* nextVisibleTime
* ID
* popReceipt
* dequeueCount
* queueTrigger (outra maneira de recuperar o texto da mensagem de fila como uma cadeia de caracteres)

Confira como usar os metadados de fila no [exemplo de gatilho](#triggersample)

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Exemplo de gatilho
Suponha que você tem o seguinte function.json, que define um gatilho da fila do Armazenamento:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

Confira o exemplo específico a um idioma que recupera e registra os metadados da fila.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemplo de gatilho em C# #
```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemplo de gatilho em Node.js

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-queue-output-binding"></a>Associação de saída da fila de armazenamento
A associação de saída da fila do Armazenamento do Azure permite que você escreva mensagens em uma fila do Armazenamento em sua função. 

A saída da fila do Armazenamento de uma função usa os seguintes objetos JSON na matriz `bindings` de function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
    "queueName": "<Name of queue to write to>",
    "connection":"<Name of app setting - see below>",
  "type": "queue",
  "direction": "out"
}
```

`connection` deve conter o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. No Portal do Azure, o editor padrão na guia **Integrar** define essa configuração de aplicativo para você ao criar uma conta de armazenamento ou selecionar uma conta existente. Para criar manualmente essa configuração de aplicativo, veja [Gerenciar as configurações do Serviço de Aplicativo](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings).

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de saída
Nas funções do C#, escreva uma mensagem de fila usando o parâmetro `out` nomeado na assinatura da função, como `out <T> <name>`. Nesse caso, `T` é o tipo de dados que deseja serializar a mensagem, e `paramName` é o nome que você especificou no [ligação de saída](#output). Nas funções de Node.js, acesse a saída usando `context.bindings.<name>`.

Você pode exibir uma mensagem da fila usando qualquer um dos tipos de dados no seu código:

* Qualquer [objeto](https://msdn.microsoft.com/library/system.object.aspx):`out MyCustomType paramName`  
Usado para serialização JSON.  Quando você declara um tipo de saída personalizada, o tempo de execução tenta serializar o objeto em JSON. Se o parâmetro de saída for nulo quando a função for encerrada, o tempo de execução criará uma mensagem da fila como um objeto nulo.
* Cadeia de caracteres: `out string paramName`  
Usado para mensagens de teste. o tempo de execução só criará uma mensagem quando o parâmetro da cadeia de caracteres não for nulo quando a função for encerrada.
* Matriz de bytes: `out byte[]` 

Esses tipos de saída adicionais são suportados uma função c#:

* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx): `out CloudQueueMessage` 
* `ICollector<T>`ou `IAsyncCollector<T>` onde `T` é um dos tipos com suporte.

<a name="outputsample"></a>

## <a name="output-sample"></a>Amostra de saída
Suponha que você tem o function.json a seguir, que define um [gatilho de fila de armazenamento](functions-bindings-storage-queue.md), uma entrada de blob de armazenamento e uma saída de blob de armazenamento:

O *function.json* de exemplo para uma associação de saída de fila de armazenamento que usa um gatilho manual e grava a entrada em uma mensagem de fila:

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "queue",
      "name": "myQueueItem",
      "queueName": "myqueue",
      "connection": "my_storage_connection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Confira o exemplo específico a um idioma que grava uma mensagem da fila de saída para cada mensagem da fila de entrada.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemplo de saída em C# #

```cs
public static void Run(string input, out string myQueueItem, TraceWriter log)
{
    myQueueItem = "New message: " + input;
}
```

Ou, para enviar várias mensagens,

```cs
public static void Run(string input, ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Message 1: " + input);
    myQueueItem.Add("Message 2: " + "Some other message.");
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Amostra de saída em Node.js

```javascript
module.exports = function(context) {
    // Define a new message for the myQueueItem output binding.
    context.bindings.myQueueItem = "new message";
    context.done();
};
```

Ou, para enviar várias mensagens,

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas

Para obter um exemplo de uma função que usa gatilhos de fila aStorage e associações, consulte [criar uma função do Azure conectada a um serviço do Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Jan17_HO3-->


