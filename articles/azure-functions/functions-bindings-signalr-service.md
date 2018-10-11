---
title: Associações do Serviço SignalR do Azure Functions
description: Entenda como usar associações do Serviço SignalR com o Azure Functions.
services: functions
documentationcenter: na
author: anthonychu
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/23/2018
ms.author: antchu
ms.openlocfilehash: 2892481dca9ce62d96e954656341925b4c8110f9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802008"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Associações do Serviço SignalR para o Azure Functions

Este artigo explica como autenticar e enviar mensagens em tempo real para clientes conectados ao [Serviço do Azure SignalR](https://azure.microsoft.com/services/signalr-service/) usando associações do Serviço SignalR no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para o Serviço SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Pacotes - Functions 2. x

As associações do Serviço SignalR são fornecidas no pacote do NuGet [Microsoft.Azure.WebJobs.Extensions.SignalRService](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService), versão 1.0.0-preview1-*. O código-fonte do pacote está no repositório GitHub [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

> [!NOTE]
> O Serviço Azure SignalR está disponível ao público em geral. No entanto, as associações do Serviço SignalR para Azure Functions estão atualmente em versão prévia.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>Associação de entrada de informações da conexão do SignalR

Antes que um cliente possa se conectar ao serviço do Azure SignalR, ele deve recuperar a URL do ponto de extremidade de serviço e um token de acesso válido. A associação de entrada *SignalRConnectionInfo* produz a URL do ponto de extremidade de Serviço SignalR e um token válido que são usados para se conectar ao serviço. Como o token é limitada pelo tempo e pode ser usado para autenticar um usuário específico para uma conexão, você não deve armazenar em cache o token nem o compartilhar entre clientes. Um gatilho HTTP que use essa associação pode ser usado pelos clientes para recuperar as informações de conexão.

Consulte o exemplo específico a um idioma:

* [2.x C#](#2x-c-input-example)
* [2.x JavaScript](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>2.x Exemplo de entrada em C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adquire as informações de conexão do SignalR usando a associação de entrada e retorna-a via HTTP.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de função usando [Autenticação de Serviço de Aplicativo] (../app-service/app-service-authentication-overview.md).

Autenticação do Serviço de Aplicativo define os cabeçalhos HTTP denominados `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm a ID e o nome da entidade de segurança do cliente do usuário autenticado, respectivamente. Você pode definir a propriedade `UserId` da associação como o valor do cabeçalho usando uma [expressão de associação](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>2.x Exemplo de entrada em JavaScript

O exemplo a seguir mostra uma associação de entrada de informações de conexão do SignalR em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação para retornar as informações de conexão.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>Tokens autenticados

Se a função for disparada por um cliente autenticado, você poderá adicionar uma declaração de ID de usuário ao token gerado. Você pode adicionar facilmente a autenticação a um aplicativo de função usando [Autenticação de Serviço de Aplicativo] (../app-service/app-service-authentication-overview.md).

Autenticação do Serviço de Aplicativo define os cabeçalhos HTTP denominados `x-ms-client-principal-id` e `x-ms-client-principal-name` que contêm a ID e o nome da entidade de segurança do cliente do usuário autenticado, respectivamente. Você pode definir a propriedade `userId` da associação como o valor do cabeçalho usando uma [expressão de associação](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

function.json de exemplo:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>Associação de saída do SignalR

Use a associação de saída do *SignalR* para enviar uma ou mais mensagens usando o Serviço Azure SignalR. Você pode difundir uma mensagem a todos os clientes conectados, ou pode difundi-la somente para clientes conectados que foram autenticados em um determinado usuário.

Consulte o exemplo específico a um idioma:

* [2.x C#](#2x-c-output-example)
* [2.x JavaScript](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>2.x Exemplo de saída em C#

#### <a name="broadcast-to-all-clients"></a>Difundir para todos os clientes

A exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que envia uma mensagem usando a associação de saída para todos os clientes conectados. O `Target` é o nome do método a ser invocado em cada cliente. A propriedade `Arguments` é uma matriz de zero ou mais objetos a serem passados para o método do cliente.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>Enviar para um usuário

Você pode enviar uma mensagem apenas para conexões autenticadas para um usuário configurando a propriedade `UserId` da mensagem do SignalR.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>2.x Exemplo de saída em JavaScript

#### <a name="broadcast-to-all-clients"></a>Difundir para todos os clientes

O exemplo a seguir mostra uma associação de saída do SignalR em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação para enviar uma mensagem com o Serviço do Azure SignalR. Defina a associação de saída para uma matriz de uma ou mais mensagens do SignalR. Uma mensagem do SignalR consiste em uma propriedade `target` que especifica o nome do método a ser invocado em cada cliente, e uma propriedade `arguments` que é uma matriz de objetos a serem passados para o método de cliente como argumentos.

Aqui estão os dados de associação no arquivo *function.json*:

function.json de exemplo:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>Enviar para um usuário

Você pode enviar uma mensagem apenas para conexões autenticadas para um usuário configurando a propriedade `userId` da mensagem do SignalR.

*function.json* permanece igual. Aqui está o código JavaScript:

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>Configuração

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `SignalRConnectionInfo`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo**|| Deve ser definido como `signalRConnectionInfo`.|
|**direction**|| Deve ser definido como `in`.|
|**name**|| Nome da variável usada no código de função para o objeto de informações de conexão. |
|**hubName**|**HubName**| Esse valor deve ser definido como o nome do hub SignalR para o qual as informações de conexão são geradas.|
|**userId**|**UserId**| Opcional: o valor da declaração do identificador de usuário a ser definida no token de chave de acesso. |
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da configuração do aplicativo que contém a cadeia de conexão do SignalR Service (o padrão é "AzureSignalRConnectionString") |

### <a name="signalr"></a>SignalR

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `SignalR`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo**|| Deve ser definido como `signalR`.|
|**direction**|| Deve ser definido como `out`.|
|**name**|| Nome da variável usada no código de função para o objeto de informações de conexão. |
|**hubName**|**HubName**| Esse valor deve ser definido como o nome do hub SignalR para o qual as informações de conexão são geradas.|
|**connectionStringSetting**|**ConnectionStringSetting**| O nome da configuração do aplicativo que contém a cadeia de conexão do SignalR Service (o padrão é "AzureSignalRConnectionString") |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

