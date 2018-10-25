---
title: Lidando com eventos externos nas Funções Duráveis – Azure
description: Saiba como lidar com eventos externos na extensão de Funções Duráveis do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: b1d47c495d24a40f254279db0e9db12a659c861c
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237714"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Lidando com eventos externos nas Funções Duráveis (Azure Functions)

Funções de orquestrador têm a capacidade de aguardar e escutar eventos externos. Frequentemente, esse recurso das [Funções Duráveis](durable-functions-overview.md) é útil para lidar com interação humana ou com outros gatilhos externos.

## <a name="wait-for-events"></a>Aguardar eventos

O método [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) permite que uma função de orquestrador aguarde e monitore de forma assíncrona um evento externo. A função do orquestrador que está escutando declara o *nome* do evento e a *forma dos dados* que espera receber.

#### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

O exemplo anterior escuta um único evento específico e entra em ação quando ele é recebido.

Você pode escutar vários eventos ao mesmo tempo, como no exemplo a seguir, que aguarda uma de três notificações de evento possíveis.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

O exemplo anterior escuta *qualquer* um de vários eventos. Também é possível aguardar *todos* os eventos.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)

```javascript
const df = require.orchestrator("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivityAsync("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) aguarda indefinidamente por uma entrada.  O aplicativo de funções pode ser descarregado com segurança enquanto aguarda. Se um evento chegar para esta instância de orquestração, ela será ativada automaticamente e processará imediatamente o evento.

> [!NOTE]
> Se o seu aplicativo de funções utiliza o Plano de Consumo, não são feitas cobranças enquanto uma função de orquestrador está aguardando uma tarefa de `WaitForExternalEvent`, independentemente do tempo de espera.

No .NET, se a carga do evento não puder ser convertida no `T` do tipo esperado, uma exceção será lançada.

## <a name="send-events"></a>Enviar eventos

O método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) envia os eventos que `WaitForExternalEvent` aguarda.  O método `RaiseEventAsync` usa *eventName* e *eventData* como parâmetros. Os dados do evento devem ser serializáveis em JSON.

Abaixo, temos um exemplo de função disparada em fila que envia um evento de "Aprovação" para uma instância de função de orquestrador. A ID da instância de orquestração vem do corpo da mensagem da fila.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)
Em JavaScript, teremos que invocar uma API REST para acionar um evento para o qual a função durável está aguardando.
Este exemplo abaixo usa o pacote de "solicitação". O método abaixo pode ser usado para acionar eventos para qualquer instância de função durável

```js
function raiseEvent(instanceId, eventName) {
        var url = `<<BASE_URL>>/runtime/webhooks/durabletask/instances/${instanceId}/raiseEvent/${eventName}?taskHub=DurableFunctionsHub`;
        var body = <<BODY>>
            
        return new Promise((resolve, reject) => {
            request({
                url,
                json: body,
                method: "POST"
            }, (e, response) => {
                if (e) {
                    return reject(e);
                }

                resolve();
            })
        });
    }
```

<<BASE_URL>> será a url base de seu aplicativo de funções. Se você estiver executando o código localmente, ele será algo parecido com http://localhost:7071 ou, no Azure, como https://<<functionappname>>.azurewebsites.net


Internamente, `RaiseEventAsync` enfileira uma mensagem que é obtida pela função de orquestrador em espera.

> [!WARNING]
> Se não houver nenhuma instância de orquestração com a *ID de instância* especificada ou se a instância não estiver aguardando o *nome do evento* especificado, a mensagem de evento será descartada. Para obter mais informações sobre esse comportamento, consulte o [Problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como configurar orquestrações eternas](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda eventos externos](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Executar um exemplo que aguarda interação humana](durable-functions-phone-verification.md)

