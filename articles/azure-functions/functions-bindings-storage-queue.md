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
ms.date: 11/02/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 36cf563a8318acb9371c48ba7d29e24694446e45


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

`connection` deve conter o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. No Portal do Azure, o editor padrão na guia **Integrar** define essa configuração de aplicativo para você ao criar uma conta de armazenamento ou selecionar uma conta existente. Para criar essa configuração de aplicativo manualmente, confira [configure this app setting manually]() (definir essa configuração de aplicativo manualmente).

[Configurações adicionais](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) podem ser fornecidas em um arquivo host.json para ajustar ainda mais gatilhos da fila de armazenamento.  

### <a name="handling-poison-queue-messages"></a>Tratamento de mensagens suspeitas na fila
Quando uma função do gatilho de fila falhar, o Azure Functions repetirá essa função até cinco vezes por padrão (incluindo a primeira tentativa) para uma determinada mensagem da fila. Se todas as cinco tentativas falharem, o Functions adicionará uma mensagem em uma fila do Armazenamento chamada *&lt;originalqueuename>-poison*. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária. 

Se quiser manipular mensagens suspeitas manualmente, você poderá obter o número de vezes que uma mensagem foi selecionada para processamento ao verificar `dequeueCount` (confira [Queue trigger metadata](#meta) (Enfileirar metadados de gatilho)).

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso de gatilho
Nas funções do C#, você se vincula à mensagem de entrada usando um parâmetro nomeado na assinatura da função, como `<T> <name>`.
Onde `T` é o tipo de dados em que você deseja desserializar os dados e `paramName` é o nome especificado na [associação de gatilho](#trigger). Nas funções do Node.js, acesse os dados de blob de entrada usando `context.bindings.<name>`.

A mensagem da fila pode ser desserializada para qualquer um destes tipos:

* Qualquer [objeto](https://msdn.microsoft.com/library/system.object.aspx) – útil para mensagens serializadas para JSON.
  Se você declarar um tipo de entrada personalizado (por exemplo, `FooType`), o Azure Functions tentará desserializar os dados JSON para o tipo especificado.
* Cadeia de caracteres
* Matriz de bytes 
* `CloudQueueMessage` (C#) 

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
    context.log('Node.js queue trigger function processed work item' context.bindings.myQueueItem);
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

`connection` deve conter o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. No Portal do Azure, o editor padrão na guia **Integrar** define essa configuração de aplicativo para você ao criar uma conta de armazenamento ou selecionar uma conta existente. Para criar essa configuração de aplicativo manualmente, confira [configure this app setting manually]() (definir essa configuração de aplicativo manualmente).

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso da saída
Nas funções do C#, escreva uma mensagem da fila usando o parâmetro `out` nomeado em sua assinatura da função, como `out <T> <name>`, onde `T` é o tipo de dados em que você deseja serializar a mensagem, e `paramName` é o nome especificado na [associação de saída](#output). Nas funções de Node.js, acesse a saída usando `context.bindings.<name>`.

Você pode exibir uma mensagem da fila usando qualquer um dos tipos de dados no seu código:

* Qualquer [objeto](https://msdn.microsoft.com/library/system.object.aspx) – útil para serialização JSON.
  Se você declarar um tipo de saída personalizada (por exemplo, `out FooType paramName`), o Azure Functions tentará serializar o objeto em JSON. Se o parâmetro de saída for nulo quando a função for encerrada, o tempo de execução do Functions criará uma mensagem da fila como um objeto nulo.
* Cadeia de caracteres – (`out string paramName`) útil para mensagens de teste. o tempo de execução do Functions criará uma mensagem somente se o parâmetro da cadeia de caracteres não for nulo quando a função for encerrada.
* Matriz de bytes – (`out byte[]`) 
* `out CloudQueueMessage` – somente C# 

No C#, você também pode se associar a `ICollector<T>` ou `IAsyncCollector<T>`, onde `T` é um dos tipos com suporte.

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemplo de saída
Suponha que você tem o function.json a seguir, que define um [gatilho da fila do Armazenamento](functions-bindings-storage-queue.md), uma entrada de blob do Armazenamento e uma saída de blob do Armazenamento:

O *function.json* de exemplo para uma associação de saída de fila de armazenamento que usa um gatilho de fila e grava uma mensagem de fila:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
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
public static void Run(string myQueueItem, out string myQueue, TraceWriter log)
{
    myQueue = myQueueItem + "(next step)";
}
```

Ou, para enviar várias mensagens,

```cs
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
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
    context.bindings.myQueue = context.bindings.myQueueItem + "(next step)";
    context.done();
};
```

Ou, para enviar várias mensagens,

```javascript
module.exports = function(context) {
    context.bindings.myQueue = [];

    context.bindings.myQueueItem.push("(step 1)");
    context.bindings.myQueueItem.push("(step 2)");
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


