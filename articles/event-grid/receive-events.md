---
title: Receber eventos da Grade de Eventos do Azure em um ponto de extremidade HTTP
description: Descreve como validar um ponto de extremidade HTTP, receber e desserializar os eventos da Grade de Eventos do Azure
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: article
ms.date: 02/16/2018
ms.author: babanisa
ms.openlocfilehash: da4d1aff45c39d8b4ce7cdd003dd4af35dcd33b3
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="receive-events-to-an-http-endpoint"></a>Receber eventos em um ponto de extremidade HTTP

Este artigo descreve como [validar um ponto de extremidade HTTP](security-authentication.md#webhook-event-delivery) para receber eventos de uma Assinatura de Evento e, em seguida, receber e desserializar os eventos. Este artigo usa uma função do Azure para fins de demonstração. No entanto, os mesmos conceitos se aplicam independentemente de onde o aplicativo está hospedado.

> [!NOTE]
> É **fortemente** recomendado que você use um [Gatilho da Grade de Eventos](../azure-functions/functions-bindings-event-grid.md) ao disparar uma função do Azure com a Grade de Eventos. O uso de um gatilho WebHook genérico aqui serve apenas como demonstração.

## <a name="prerequisites"></a>pré-requisitos

* Você precisará de um aplicativo de funções com uma [função disparada HTTP](../azure-functions/functions-create-generic-webhook-triggered-function.md)

## <a name="add-dependencies"></a>Adicionar dependências

Se você estiver desenvolvendo no .NET, [adicione uma dependência](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) à função para o `Microsoft.Azure.EventGrid` [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). SDKs para outros idiomas estão disponíveis na referência [SDKs de Publicação](./sdk-overview.md#publish-sdks). Esses pacotes contêm os modelos para tipos de eventos nativos, como `EventGridEvent`, `StorageBlobCreatedEventData` e `EventHubCaptureFileCreatedEventData`.

Para fazer isso, clique no link "Exibir Arquivos" no Azure Functions (painel mais à direita no portal de funções do Azure) e crie um arquivo chamado project.json. Adicione o seguinte código ao arquivo `project.json` e salve-o: 
 
 ```json
{ 
  "frameworks": { 
    "net46":{ 
      "dependencies": { 
        "Microsoft.Azure.EventGrid": "1.1.0-preview" 
      } 
    } 
   } 
} 
```

![Pacote NuGet adicionado](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Validação do ponto de extremidade

A primeira coisa que queremos fazer é manipular eventos `Microsoft.EventGrid.SubscriptionValidationEvent`. Sempre que uma nova Assinatura de Evento for criada, a Grade de Eventos envia um evento de validação para o ponto de extremidade com um `validationCode` na carga de dados. O ponto de extremidade é necessário para ecoar no corpo da resposta a fim de [provar que o ponto de extremidade é válido e pertence a você](security-authentication.md#webhook-event-delivery). Se você estiver usando um [Gatilho da Grade de Eventos](../azure-functions/functions-bindings-event-grid.md) em vez de uma função disparada por WebHook, a validação do ponto de extremidade é tratada para você.

Use o seguinte código para lidar com a validação de assinatura:

```csharp
using System.Net; 
using Newtonsoft.Json; 
using Newtonsoft.Json.Linq; 
using Newtonsoft.Json.Serialization; 
using Microsoft.Azure.EventGrid.Models; 
 
class SubscriptionValidationEventData 
{ 
    public string ValidationCode { get; set; } 
} 
 
class SubscriptionValidationResponseData 
{ 
    public string ValidationResponse { get; set; } 
} 
 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) 
{ 

    log.Info($"C# HTTP trigger function begun");  
    string response = string.Empty; 
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent"; 
 
    string requestContent = await req.Content.ReadAsStringAsync(); 
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent); 
 
    foreach (EventGridEvent eventGridEvent in eventGridEvents) 
    { 
        JObject dataObject = eventGridEvent.Data as JObject; 
       
        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>(); 
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}"); 
            // Do any additional validation (as required) and then return back the below response 
            var responseData = new SubscriptionValidationResponseData(); 
            responseData.ValidationResponse = eventData.ValidationCode; 
            return req.CreateResponse(HttpStatusCode.OK, responseData);    
        }             
    } 
     
    return req.CreateResponse(HttpStatusCode.OK, response);     
} 

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
}; 
```

### <a name="test-validation-response"></a>Resposta de validação do teste

Teste a função de resposta de validação colando o evento de exemplo no campo de teste para a função:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Quando você clica em Executar, a saída deve ser 200 OK e `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` no corpo:

![resposta de validação](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Tratar dos eventos do armazenamento de blobs

Agora podemos estender a função para manipular `Microsoft.Storage.BlobCreated`:

```cs
using System.Net; 
using Newtonsoft.Json; 
using Newtonsoft.Json.Linq; 
using Newtonsoft.Json.Serialization; 
using Microsoft.Azure.EventGrid.Models; 
 
class SubscriptionValidationEventData 
{ 
    public string ValidationCode { get; set; } 
} 
 
class SubscriptionValidationResponseData 
{ 
    public string ValidationResponse { get; set; } 
} 
 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) 
{ 
    log.Info($"C# HTTP trigger function begun");  
    string response = string.Empty; 
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated"; 
 
 
    string requestContent = await req.Content.ReadAsStringAsync(); 
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent); 
 
    foreach (EventGridEvent eventGridEvent in eventGridEvents) 
    { 
        JObject dataObject = eventGridEvent.Data as JObject; 
       
        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>(); 
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response 
            var responseData = new SubscriptionValidationResponseData(); 
            responseData.ValidationResponse = eventData.ValidationCode; 
            return req.CreateResponse(HttpStatusCode.OK, responseData);    
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>(); 
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}"); 
        }    
    } 
     
    return req.CreateResponse(HttpStatusCode.OK, response);     
}

```

```javascript
var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};
```

### <a name="test-blob-created-event-handling"></a>Manipulação do evento Testar Blob Criado

Teste a nova funcionalidade da função colocando um [evento de armazenamento de blobs](./event-schema-blob-storage.md#example-event) no campo de teste e executando:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Você deve ver a saída de URL do blob no log de função:

![Log de saída](./media/receive-events/blob-event-response.png)

Você também pode testar essa saída em tempo real criando uma conta de armazenamento de blobs ou GPv2 (Uso Geral V2), [adicionando uma assinatura de evento](../storage/blobs/storage-blob-event-quickstart.md) e definindo o ponto de extremidade para a URL de função:

![URL da função](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Manipular eventos personalizados

Finalmente, vamos estender a função mais uma vez para que ela também possa tratar eventos personalizados. Adicionamos uma verificação ao nosso próprio evento `Contoso.Items.ItemReceived`. O código final deve ter esta aparência:

```cs
using System.Net; 
using Newtonsoft.Json; 
using Newtonsoft.Json.Linq; 
using Newtonsoft.Json.Serialization; 
using Microsoft.Azure.EventGrid.Models; 
 
class SubscriptionValidationEventData 
{ 
    public string ValidationCode { get; set; } 
} 
 
class SubscriptionValidationResponseData 
{ 
    public string ValidationResponse { get; set; } 
} 

class ContosoItemReceivedEventData 
{ 
    public string id { get; set; }     
    public string message { get; set; }   
    public string time { get; set; }
} 
 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) 
{ 
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty; 
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated"; 
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";
 
 
    string requestContent = await req.Content.ReadAsStringAsync(); 
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent); 
 
    foreach (EventGridEvent eventGridEvent in eventGridEvents) 
    { 
        JObject dataObject = eventGridEvent.Data as JObject; 
       
        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>(); 
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}"); 
            // Do any additional validation (as required) and then return back the below response 
            var responseData = new SubscriptionValidationResponseData(); 
            responseData.ValidationResponse = eventData.ValidationCode; 
            return req.CreateResponse(HttpStatusCode.OK, responseData);    
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>(); 
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}"); 
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>(); 
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}"); 
        } 
    } 
     
    return req.CreateResponse(HttpStatusCode.OK, response);     
}

```

```javascript
var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Testar tratamento de eventos personalizados

Por fim, teste se sua função estendida agora pode controlar o tipo de evento personalizado:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Você também pode testar essa funcionalidade em tempo real [enviando um evento personalizado com CURL no Portal](./custom-event-quickstart-portal.md) ou [publicando em um tópico personalizado](./post-to-custom-topic.md) usando serviço ou aplicativo que possa enviar POST para um ponto de extremidade, como o [Postman](https://www.getpostman.com/). Crie um tópico personalizado e uma assinatura de evento com o ponto de extremidade definido como a URL de função.

## <a name="next-steps"></a>Próximas etapas

* Explorar o [Gerenciamento da Grade de Eventos do Azure e SDKs de Publicação](./sdk-overview.md)
* Saiba como [publicar em um tópico personalizado](./post-to-custom-topic.md)
* Experimente um dos tutoriais detalhados da Grade de Eventos e do Functions, como [Redimensionando imagens carregadas no armazenamento de blobs](resize-images-on-storage-blob-upload-event.md)
