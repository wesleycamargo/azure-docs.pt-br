---
title: "Singletons para Funções Duráveis – Azure"
description: "Como usar singletons para a extensão de Funções Duráveis do Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 8384d17405653a29207cdfa4f6143504d0db2022
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2017
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orquestradores singleton em Funções Duráveis (Azure Functions)

Para trabalhos em segundo plano ou orquestrações com estilo de ator, normalmente você precisa garantir que apenas uma instância de um determinado orquestrador seja executada por vez. Isso pode ser feito nas [Funções Duráveis](durable-functions-overview.md) atribuindo uma ID de instância específica a um orquestrador ao criá-lo.

## <a name="singleton-example"></a>Exemplo de singleton

O exemplo em C# a seguir mostra uma função de gatilho HTTP que cria uma orquestração singleton de trabalho em segundo plano. O código garante que apenas uma instância exista para uma ID de instância especificada.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

Por padrão, IDs de instância são GUIDs gerados aleatoriamente. Mas, nesse caso, a ID de instância é passada nos dados da rota da URL. O código chama [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) para verificar se uma instância com a ID especificada já está em execução. Se não estiver, uma instância será criada com essa ID.

Os detalhes da implementação da função de orquestrador, na verdade, não importam. Poderia ser uma função de orquestrador regular que é iniciada e concluída ou poderia ser uma que é executada eternamente (ou seja, um [Orquestração Eterna](durable-functions-eternal-orchestrations.md)). O aspecto importante é que há somente uma instância em execução por vez.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como chamar suborquestrações](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Executar um singleton de exemplo](durable-functions-counter.md)
