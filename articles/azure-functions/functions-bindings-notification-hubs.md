---
title: "Associações de Hubs de Notificação para o Azure Functions"
description: "Entenda como usar a associação de Hub de Notificação do Azure no Azure Functions."
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
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: cbc22721bbe06fdc5a8a9026b113071e1616bcb8
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Associação de saída dos Hubs de Notificação para Azure Functions

Este artigo explica como enviar notificações por push usando associações de [Hubs de Notificação do Azure](../notification-hubs/notification-hubs-push-notification-overview.md) no Azure Functions. O Azure Functions oferece suporte a uma associação de saída para os Hubs de Notificação.

Os Hubs de Notificação do Azure devem ser configurados para os PNS (Serviços de Notificações de Plataforma) que você deseja usar. Para saber como obter notificações por push em seu aplicativo cliente desde Hubs de Notificação, veja [Introdução aos Hubs de Notificação](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e selecione a plataforma de cliente de destino na lista suspensa na parte superior da página.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example---template"></a>Exemplo - modelo

As notificações enviadas podem ser notificações nativas ou [notificações de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). As notificações nativas são destinadas a uma plataforma específica de cliente, conforme configurado na propriedade `platform` da associação de saída. Uma notificação de modelo pode ser usada para ter como destino várias plataformas.   

Consulte o exemplo específico a um idioma:

* [Script do C# - parâmetro out](#c-script-template-example---out-parameter)
* [Script do C# - assíncrono](#c-script-template-example---asynchronous)
* [Script do C# - JSON](#c-script-template-example---json)
* [Script do C# - tipos de biblioteca](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Exemplo de modelo de script do C# - parâmetro out

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um espaço reservado `message` no modelo.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

### <a name="c-script-template-example---asynchronous"></a>Exemplo de modelo de script do C# - assíncrono

Se você estiver usando o código assíncrono, os parâmetros de saída não serão permitidos. Nesse caso, use `IAsyncCollector` para retornar a notificação de modelo. O código a seguir é um exemplo de código assíncrono acima. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

### <a name="c-script-template-example---json"></a>Exemplo de modelo de script do C# - JSON

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém um espaço reservado `message` no modelo usando uma cadeia de caracteres JSON.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Exemplo de modelo script do C# - tipos de biblioteca

Este exemplo mostra como usar tipos definidos na [Biblioteca de Hubs de Notificações do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

### <a name="f-template-example"></a>Exemplo de modelo F#

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Exemplo de modelo JavaScript

Este exemplo envia uma notificação para um [registro de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="example---apns-native"></a>Exemplo - APNS nativo

Este exemplo de script C# mostra como enviar uma notificação nativa do APNS. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="example---gcm-native"></a>Exemplo - GCM nativo

Este exemplo de script C# mostra como enviar uma notificação nativa do GCM. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="example---wns-native"></a>Exemplo - WNS nativo

Este exemplo de script C# mostra como usar tipos definidos na [Biblioteca de Hubs de Notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação nativa WNS do sistema. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="attributes"></a>Atributos

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use o atributo [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs), que é definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs).

Os parâmetros e as propriedades do construtor do atributo são descritos na seção [Configuração](#configuration).

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `NotificationHub`:

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** |n/d| Deve ser definido como "notificationHub". |
|**direction** |n/d| Deve ser definido como "out". | 
|**name** |n/d| Nome da variável usada no código de função para a mensagem do hub de notificação. |
|**tagExpression** |**TagExpression** | As expressões de marca permitem que você especifique que as notificações sejam entregues a um conjunto de dispositivos registrados para receber notificações que correspondem à expressão de marca.  Para saber mais, veja [Expressões de marca e de roteamento](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nome do recurso de hub de notificação no portal do Azure. |
|**conexão** | **ConnectionStringSetting** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Hubs de Notificação.  A cadeia de caracteres de conexão deve ser definida como o valor *DefaultFullSharedAccessSignature* para o hub de notificação. Veja [Configuração da cadeia de conexão](#connection-string-setup) posteriormente neste artigo.|
|**platform** | **Plataforma** | A propriedade platform indica a plataforma de cliente à qual sua notificação se destina. Por padrão, se a propriedade da plataforma é omitida da associação de saída, as notificações de modelo podem ser usadas para atingir qualquer plataforma configurada no Hub de Notificação do Azure. Para obter mais informações sobre como usar modelos em geral para enviar várias notificações de plataforma com um Hub de Notificação do Azure, consulte [Modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Quando definida, **platform** deve ser um dos seguintes valores: <ul><li><code>apns</code>&mdash;Apple Push Notification Service. Para obter mais informações sobre como configurar o hub de notificação do APNS e receber a notificação em um aplicativo cliente, consulte [Enviar notificações por push para iOS com os Hubs de Notificação do Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>&mdash;[Amazon Device Messaging](https://developer.amazon.com/device-messaging). Para obter mais informações sobre como configurar o hub de notificação para ADM e receber a notificação de um aplicativo Kindle, consulte [Introdução aos Hubs de Notificação para aplicativos Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>&mdash;[Google Cloud Messaging](https://developers.google.com/cloud-messaging/). Também há suporte para o Firebase Cloud Messaging, que é a nova versão do GCM. Para mais informações, consulte [Como enviar notificações por push para Android com Hubs de Notificação do Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>&mdash;[Serviços de Notificação por Push do Windows](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) visando plataformas Windows. Também há suporte para Windows Phone 8.1 e posterior pelo WNS. Para mais informações, consulte [Introdução aos Hubs de Notificação para aplicativos da Plataforma Universal do Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>&mdash;[Serviço de Notificação por Push da Microsoft](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Essa plataforma dá suporte a plataformas mais antigas do Windows Phone e Windows Phone 8. Para mais informações, consulte [Como enviar notificações por push com Hubs de Notificação do Azure no Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Exemplo de arquivo function.json

Aqui está um exemplo de uma associação de Hubs de Notificação em um arquivo *function.json*.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Configuração de cadeia de conexão

Para usar uma associação de saída de um hub de notificação, você deve configurar a cadeia de conexão para o hub. Você pode selecionar um hub de notificação existente ou criar um novo diretamente na guia *Integrar* no portal do Azure. Você também pode configurar a cadeia de conexão manualmente. 

Para configurar a cadeia de conexão em um hub de notificação existente:

1. Navegue até seu hub de notificação no [portal do Azure](https://portal.azure.com), escolha **Políticas de acesso** e selecione o botão Copiar ao lado da política **DefaultFullSharedAccessSignature**. Isso copia a cadeia de conexão para a política *DefaultFullSharedAccessSignature* ao hub de notificação. Essa cadeia de conexão permite que a função envie mensagens de notificação para o hub.
    ![Copiar a cadeia de conexão do hub de notificação](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Navegue até seu aplicativo de funções no portal do Azure, escolha **Configurações do aplicativo**, adicione uma chave como **MyHubConnectionString**, cole o *DefaultFullSharedAccessSignature* copiado em seu hub de notificação como o valor e, em seguida, clique em **Salvar**.

O nome dessa configuração de aplicativo é o que está na configuração de conexão de associação de saída em *function.json* ou mo atributo do .NET. Veja a [seção Configuração](#configuration) anteriormente neste artigo.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

