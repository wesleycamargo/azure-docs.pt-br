---
title: Gatilho de Grade de Eventos para o Azure Functions
description: Entenda como manipular a Grade de Eventos no Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: e1d623c831a912598db72ccd0242cf827c88ee6c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-trigger-for-azure-functions"></a>Gatilho de Grade de Eventos para o Azure Functions

Este artigo explica como manipular com eventos de [Grade de Eventos](../event-grid/overview.md) no Azure Functions.

A Grade de Eventos é um serviço do Azure que envia solicitações HTTP para notificá-lo sobre eventos que acontecem nos *publicadores*. Um publicador é o serviço ou recurso que origina o evento. Por exemplo, uma conta de armazenamento de Blobs do Azure é um publicador, e uma exclusão ou upload de blob é um evento. Alguns [serviços do Azure têm suporte interno para publicar eventos na Grade de Eventos](../event-grid/overview.md#event-publishers). 

Os *manipuladores* de eventos recebem e processam eventos. O Azure Functions é um dos vários serviços do[Azure que possuem suporte interno para manipular eventos da Grande de Eventos](../event-grid/overview.md#event-handlers). Neste artigo, você aprende a usar um gatilho de Grade de Eventos para invocar uma função quando um evento é recebido da Grade de Eventos.

Se você preferir, é possível utilizar um gatilho HTTP para manipular eventos da Grade de Eventos, consultado [Usar um gatilho HTTP como um gatilho de Grade de Eventos](#use-an-http-trigger-as-an-event-grid-trigger), posteriormente neste artigo.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Exemplo

Consulte o exemplo específico do idioma para um gatilho de Grade de Eventos:

* [C#](#c-example)
* [Script do C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

Para um exemplo de gatilho HTTP, consulte [Como usar o gatilho HTTP](#use-an-http-trigger-as-an-event-grid-trigger), posteriormente neste artigo.

### <a name="c-example"></a>Exemplo de C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que registra alguns dos campos comuns a todos os eventos e a todos os dados específicos do evento.

```cs
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```

O atributo `EventGridTrigger` está definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid).

### <a name="c-script-example"></a>Exemplo 2 de C# script

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função de script de C#](functions-reference-csharp.md) que usa a associação. A função registra alguns dos campos comuns a todos os eventos e a todos os dados específicos do evento.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.Azure.WebJobs.Extensions.EventGrid"
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função registra alguns dos campos comuns a todos os eventos e a todos os dados específicos do evento.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>Atributos

Em [bibliotecas de classe C#](functions-dotnet-class-library.md), use o atributo [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs), definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid).

Aqui está um atributo `EventGridTrigger` em uma assinatura de método:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] EventGridEvent eventGridEvent, TraceWriter log)
{
    ...
}
 ```

Para ver um exemplo completo, consulte [Exemplo de C#](#c-example).

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*. Não há parâmetros ou propriedades do construtor para definir o atributo `EventGridTrigger`.

|Propriedade function.json |DESCRIÇÃO|
|---------|---------|----------------------|
| **tipo** | Obrigatório – deve ser definido como `eventGridTrigger`. |
| **direction** | Obrigatório – deve ser definido como `in`. |
| **name** | Obrigatório - o nome da variável usado no código de função para o parâmetro que recebe os dados de eventos. |

## <a name="usage"></a>Uso

Para funções C# e F#, declare o tipo da entrada de gatilho para ser `EventGridEvent` ou um tipo personalizado. Para um tipo personalizado, o tempo de execução das Funções tenta analisar o evento JSON para definir as propriedades do objeto.

Para funções JavaScript, o parâmetro nomeado pela propriedade *function.json* `name` tem uma referência ao objeto de evento.

## <a name="event-schema"></a>Esquema do evento

Os dados de uma Grade de Eventos são recebidos como um objeto JSON no corpo de uma solicitação HTTP. O JSON é semelhante ao exemplo a seguir:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

O exemplo mostrado é uma matriz de um elemento. A Grade de Eventos envia sempre uma matriz e pode enviar mais de um evento na matriz. O tempo de execução invoca sua função uma vez para cada elemento da matriz.

As propriedades de nível superior nos dados JSON de evento serão as mesmas entre todos os tipos de eventos, enquanto os conteúdos da propriedade `data` estiverem especificados para cada tipo de evento. O exemplo mostrado é para um evento de armazenamento de Blobs.

Para obter explicações sobre as propriedades comuns e específicas de evento, consulte [Propriedades do evento](../event-grid/event-schema.md#event-properties) na documentação da Grade de Eventos.

O tipo `EventGridEvent` define apenas as propriedades de nível superior; a propriedade `Data` é um `JObject`. 

## <a name="create-a-subscription"></a>Criar uma assinatura

Para iniciar o recebimento de solicitações HTTP de Grade de Eventos, crie uma assinatura na Grade de Eventos que especifique a URL do ponto de extremidade que invoca a função.

### <a name="azure-portal"></a>Portal do Azure

Para as funções que você desenvolve no Portal do Azure com o gatilho de Grade de Eventos, selecione **Adicionar assinatura da Grade de Eventos**.

![Criar assinatura no portal](media/functions-bindings-event-grid/portal-sub-create.png)

Ao selecionar esse link, o portal abrirá a página **Criar Assinatura de Evento**  com a URL do ponto de extremidade preenchida.

![URL do ponto de extremidade preenchida](media/functions-bindings-event-grid/endpoint-url.png)

Para obter mais informações sobre como criar assinaturas usando o Portal do Azure, consulte [Criar evento personalizado - Portal do Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da Grade de Eventos.

### <a name="azure-cli"></a>CLI do Azure

Para criar uma assinatura usando [a CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), use o comando [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create).

O comando requer a URL do ponto de extremidade que invoca a função. O exemplo a seguir mostra o padrão de URL:

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

A chave do sistema é uma chave de autorização que deve ser incluída na URL do ponto de extremidade para um gatilho de Grade de Eventos. A seção a seguir explica como obter a chave do sistema.

Apresentamos aqui um exemplo que assina em uma conta de armazenamento de Blobs (com um espaço reservado para a chave do sistema):

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

Para obter mais informações sobre como criar uma assinatura, consulte o [Guia de início rápido do armazenamento de blobs](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) ou outros guias de início rápido da Grade de Eventos.

### <a name="get-the-system-key"></a>Obter a chave do sistema

Você pode obter a chave do sistema usando a seguinte API (HTTP GET):

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

Essa é uma API de administração, portanto, requer sua [chave de administração](functions-bindings-http-webhook.md#authorization-keys). Não confunda a chave do sistema (para invocar uma função de ativação da Grade de Eventos) com a chave de administração (para executar tarefas administrativas no aplicativo de funções). Ao assinar em um tópico da Grade de Eventos, certifique-se de usar a chave do sistema.

Aqui, está um exemplo da resposta que fornece a chave do sistema:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Para obter mais informações, consulte [Chaves de autorização](functions-bindings-http-webhook.md#authorization-keys) no artigo de referência de gatilho HTTP. 

Como alternativa, você mesmo pode enviar uma HTTP PUT para especificar o valor da chave.

## <a name="local-testing-with-requestbin"></a>Testes locais com RequestBin

Para testar um gatilho de Grade de Eventos localmente, você deve receber solicitações HTTP de Grade de Eventos entre suas origens na nuvem para sua máquina local. Uma maneira de fazer isso é capturar solicitações online e manualmente reenviá-las em sua máquina local:

2. [Criar um ponto de extremidade RequestBin](#create-a-RequestBin-endpoint).
3. [Criar uma assinatura na Grade de Eventos](#create-an-event-grid-subscription) que envia eventos para o ponto de extremidade RequestBin.
4. [Gerar uma solicitação](#generate-a-request) e copiar o corpo da solicitação no site RequestBin.
5. [Postar manualmente a solicitação](#manually-post-the-request) para a URL localhost da sua função de gatilho da Grade de Eventos.

Quando terminar de testar, você poderá usar a mesma assinatura para a produção atualizando o ponto de extremidade. Use o comando da CLI do Azure[az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update).

### <a name="create-a-requestbin-endpoint"></a>Criar um ponto de extremidade RequestBin

RequestBin é uma ferramenta de software livre que aceita solicitações HTTP e mostra o corpo da solicitação. A URL http://requestb.in obtém tratamento especial pela Grade de Eventos do Azure. Para facilitar o teste, a Grade de Eventos envia eventos para a URL RequestBin, sem requerer uma resposta correta às solicitações de validação de assinatura. Duas outras ferramentas de teste recebem o mesmo tratamento: http://webhookinbox.com e http://hookbin.com.

RequestBin não se destina a um uso de alta raxa de transferência. Se você efetuar push de mais de um evento por vez, talvez não veja todos os eventos na ferramenta.

Crie um ponto de extremidade.

![Criar um ponto de extremidade RequestBin](media/functions-bindings-event-grid/create-requestbin.png)

Copie a URL do ponto de extremidade.

![Copiar o ponto de extremidade RequestBin](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Criar uma assinatura na Grade de Eventos

Crie uma assinatura na Grade de Eventos do tipo que deseja testar e forneça o ponto de extremidade RequestBin. Para obter informações sobre como criar uma assinatura, consulte [Criar uma assinatura](#create-a-subscription), posteriormente neste artigo.

### <a name="generate-a-request"></a>Gerar uma solicitação

Dispare um evento que gerará tráfego HTTP para seu ponto de extremidade RequestBin.  Por exemplo, se você criou uma assinatura de armazenamento de Blobs, faça upload ou exclua um blob. Quando uma solicitação aparecer na sua página RequestBin, copie o corpo da solicitação.

A solicitação de validação de assinatura será recebida primeiro. Ignore quaisquer solicitações de validação e copie a solicitação de evento.

![Copiar o corpo da solicitação de RequestBin](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>Postar manualmente a solicitação

Execute sua função de Grade de Eventos localmente.

Use uma ferramenta como [Postman](https://www.getpostman.com/) ou [curl](https://curl.haxx.se/docs/httpscripting.html) para criar uma solicitação HTTP POST:

* Defina um cabeçalho `Content-Type: application/json`.
* Defina um cabeçalho `aeg-event-type: Notification`.
* Cole os dados RequestBin no corpo da solicitação. 
* Poste na URL da sua função de gatilho de Grade de Eventos, usando o seguinte padrão:

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={methodname}
``` 

O parâmetro `functionName` deverá ser o nome do método, e não o nome especificado no atributo `FunctionName`. Por esse motivo, se você tiver várias funções em um projeto, elas precisarão ter nomes de métodos exclusivos (não todas nomeadas `Run`) para testes locais de gatilho de Grade de Eventos.

As capturas de tela a seguir mostram os cabeçalhos e o corpo da solicitação em Postman:

![Cabeçalhos em Postman](media/functions-bindings-event-grid/postman2.png)

![Corpo da solicitação em Postman](media/functions-bindings-event-grid/postman.png)

A função de gatilho da Grade de Eventos executa e mostra logs semelhantes ao exemplo a seguir:

![Amostra de logs da função de gatilho de Grade de Eventos](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Teste local com ngrok

Outra maneira de testar um gatilho de Grade de Eventos localmente é automatizar a conexão HTTP entre a Internet e o computador de desenvolvimento. Você pode fazer isso com uma ferramenta de software livre nomeada [ngrok](https://ngrok.com/):

3. [Criar um ponto de extremidade ngrok](#create-an-ngrok-endpoint).
4. [Executar a função de gatilho de Grade de Eventos ](#run-the-event-grid-trigger-function).
5. [Criar uma assinatura na Grade de Eventos](#create-a-subscription) que envia eventos para o ponto de extremidade ngrok.
6. [Disparar um evento](#trigger-an-event).

Quando terminar de testar, você poderá usar a mesma assinatura para a produção atualizando o ponto de extremidade. Use o comando da CLI do Azure[az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update).

### <a name="create-an-ngrok-endpoint"></a>Criar um ponto de extremidade ngrok

Faça o download do *ngrok.exe* do [ngrok](https://ngrok.com/) e execute com o seguinte comando:

```
ngrok http -host-header=localhost 7071
```

O parâmetro -host-header é necessário porque o tempo de execução das funções espera solicitações do localhost quando é executado no localhost. 7071 é o número de porta padrão quando o tempo de execução é executado localmente.

O comando cria saída semelhante à seguinte:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Você usará a URL https://{subdomain}.ngrok.io para sua assinatura na Grade de Eventos.

### <a name="run-the-event-grid-trigger-function"></a>Executar a função de gatilho de Grade de Eventos

A URL ngrok não recebe tratamento especial pela Grade de Eventos, portanto, sua função deverá ser executada localmente quando a assinatura for criada. Caso contrário, a resposta de validação não será enviada e a criação da assinatura falhará.

### <a name="create-a-subscription"></a>Criar uma assinatura

Crie uma assinatura na Grade de Eventos do tipo que deseja testar e forneça o ponto de extremidade ngrok, usando o padrão a seguir:

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={methodname}
``` 

O parâmetro `functionName` deverá ser o nome do método, e não o nome especificado no atributo `FunctionName`. Por esse motivo, se você tiver várias funções em um projeto, elas precisarão ter nomes de métodos exclusivos (não todas nomeadas `Run`) para testes locais de gatilho de Grade de Eventos.

Aqui, está um exemplo usando a CLI do Azure:

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Para obter informações sobre como criar uma assinatura, consulte [Criar uma assinatura](#create-a-subscription), posteriormente neste artigo.

### <a name="trigger-an-event"></a>Disparar um evento

Dispare um evento que gerará tráfego HTTP para o ponto de extremidade ngrok.  Por exemplo, se você criou uma assinatura de armazenamento de Blobs, faça upload ou exclua um blob.

A função de gatilho da Grade de Eventos executa e mostra logs semelhantes ao exemplo a seguir:

![Amostra de logs da função de gatilho de Grade de Eventos](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Use um gatilho HTTP como um gatilho de Grade de Eventos

Os eventos da Grade de Eventos são recebidos como solicitações HTTP, para que você possa manipular eventos usando um gatilho HTTP em vez de um gatilho de Grade de Eventos. Um possível motivo para isso é obter mais controle sobre a URL do ponto de extremidade que invoca a função. 

Se você usar um gatilho HTTP, será necessário gravar o código para o que o gatilho da Grade de Eventos automaticamente:

* Envie uma resposta de validação para [uma solicitação de validação de assinatura](../event-grid/security-authentication.md#webhook-event-delivery).
* Invoque a função uma vez por elemento da matriz de eventos contida no corpo da solicitação.

O código C# da amostra a seguir para um gatilho HTTP simula o comportamento do gatilho da Grade de Eventos:

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

O código de exemplo do exemplo a seguir para um gatilho HTTP simula o comportamento do gatilho da Grade de Eventos:

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: messages[0].data.validationCode }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

O código de manipulação de eventos está dentro do loop através da matriz `messages`.

Para obter informações sobre a URL a ser utilizada para invocar a função localmente ou quando for executada no Azure, consulte a [documentação de referência de associação de gatilho HTTP](functions-bindings-http-webhook.md) 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre a Grade de Eventos](../event-grid/overview.md)
