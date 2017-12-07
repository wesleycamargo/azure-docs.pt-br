---
title: "Associação do Twilio e Azure Functions"
description: "Entenda como usar associações Twilio com Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae97045c27f3ad8b62e7798b2060ea59ccd66ac5
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="twilio-binding-for-azure-functions"></a>Associação de Twilio para o Azure Functions

Este artigo explica como enviar mensagens de texto usando-se as associações de [Twilio](https://www.twilio.com/) no Azure Functions. O Azure Functions oferece suporte a uma associação de saída para o Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Exemplo

Consulte o exemplo específico a um idioma:

* [Pré-compilado C#](#c-example)
* [Script C#](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Exemplo de C#

A exemplo a seguir mostra uma [função C# pré-compilada](functions-dotnet-class-library.md) que envia uma mensagem de texto quando acionada por uma mensagem de fila.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

Este exemplo usa o atributo `TwilioSms` com o valor de retorno do método. Uma alternativa é usar o atributo com um parâmetro `out SMSMessage` ou um parâmetro `ICollector<SMSMessage>` ou `IAsyncCollector<SMSMessage>`.

### <a name="c-script-example"></a>Exemplo 2 de C# script

O exemplo a seguir mostra uma associação de saída de Twilio em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função usa um parâmetro `out` para enviar uma mensagem de texto.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Aqui está o código de script do C#:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Você não pode usar os parâmetros em código assíncrono. Aqui está um exemplo de código de script C# assíncrono:

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo a seguir mostra uma associação de saída do Twilio em um arquivo *function.json* e código [script C#](functions-reference-node.md) que usa a associação.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Atributos

Para funções [C# pré-compiladas](functions-dotnet-class-library.md), use o atributo [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs), que é definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio).

Para obter informações sobre as propriedades de atributo que você pode configurar, consulte [Configuração](#configuration). Aqui está um exemplo de atributo `TwilioSms` em uma assinatura de método:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(
    AccountSidSetting = "TwilioAccountSid", 
    AuthTokenSetting = "TwilioAuthToken", 
    From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    ...
}
 ```

Para obter um exemplo completo, consulte [Exemplo de C# pré-compilado](#c-example).

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `TwilioSms`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo**|| deve ser definido como `twilioSms`.|
|**direction**|| deve ser definido como `out`.|
|**name**|| Nome da variável usada no código de função para a mensagem de texto SMS do Twilio. |
|**accountSid**|**AccountSid**| Esse valor deve ser definido como o nome de uma Configuração de aplicativo que contém a SID da sua conta do Twilio.|
|**authToken**|**AuthToken**| Esse valor deve ser definido como o nome de uma Configuração de aplicativo que contém seu token de autenticação do Twilio.|
|**to**|**Para**| Esse valor é definido como o número de telefone para o qual será enviada a mensagem de texto SMS.|
|**from**|**De**| Esse valor é definido como o número de telefone com o qual será enviada a mensagem de texto SMS.|
|**body**|**Corpo**| Esse valor pode ser usado para fixar a mensagem de texto SMS no código se você não precisa defini-la dinamicamente no código de sua função. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)


