---
title: Testes de unidade de Funções Duráveis do Azure
description: Saiba como testar a unidade das Funções Duráveis.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094068"
---
# <a name="durable-functions-unit-testing"></a>Testes de unidade de Funções Duráveis

Os testes de unidade são uma parte importante de modernas práticas de desenvolvimento de software. Testes de unidade verificam o comportamento de lógica de negócios e protegem contra a introdução de alterações com falhas despercebidas no futuro. As Funções Duráveis podem facilmente aumentar a complexidade, então introduzir testes de unidade ajudará a evitar alterações com falha. As seções a seguir explicam como testar a unidade dos tipos de três função - funções de Cliente orquestração, Orchestrator e Atividade.

## <a name="prerequisites"></a>Pré-requisitos

Os exemplos neste artigo requerem conhecimento sobre os conceitos e as estruturas a seguir:

* Teste de unidade

* Funções duráveis

* [xUnit](https://xunit.github.io/) - Estrutura de teste

* [moq](https://github.com/moq/moq4) - Estrutura de simulação

## <a name="base-classes-for-mocking"></a>Classes base para a simulação

Mocking é suportado através de três classes abstratas em Funções Duráveis:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Essas classes são classes base para [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) e [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), que definem os métodos Orchestration Client, Orchestrator e Activity. As simulações definirão o comportamento esperado para métodos de classe base para que o teste de unidade possa verificar a lógica de negócios. Há um fluxo de trabalho de duas etapas para testes de unidade da lógica de negócios em Cliente de Orquestração e Orchestrator:

1. Use as classes base em vez da implementação concreta ao definir assinaturas de Cliente de Orquestração e do Orchestrator.
2. Nos testes de unidade, simule o comportamento das classes base e verifique a lógica de negócios.

Encontre mais detalhes nos parágrafos a seguir para testar funções que usam a associação de cliente de orquestração e a associação de orquestrador.

## <a name="unit-testing-trigger-functions"></a>Testes de unidade das funções de gatilho

Nesta seção, o teste de unidade validará a lógica da seguinte função de gatilho HTTP para iniciar nova orquestrações.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

A tarefa de teste de unidade será verificar o valor do cabeçalho `Retry-After` fornecido na carga de resposta. Então o teste de unidade simulará alguns dos métodos [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) para garantir um comportamento previsível.

Primeiro, uma simulação de classe base é necessária, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). A simulação pode ser uma nova classe que implementa [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). No entanto, o uso de uma estrutura de simulação como [moq](https://github.com/moq/moq4) simplifica o processo:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Em seguida, o método `StartNewAsync` é simulado para retornar uma ID de instância conhecida.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Em seguida, `CreateCheckStatusResponse` é simulado para sempre retornar uma resposta HTTP 200 vazia.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`TraceWriter` também é simulado:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Agora o método `Run` é chamado do teste de unidade:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        traceWriterMock.Object);
 ```

 A última etapa é comparar o resultado com o valor esperado:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Após a combinação de todas as etapas, o teste de unidade terá o código a seguir:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Testes de unidade das funções de orquestrador

As Funções do Orchestrator são ainda mais interessantes para testes de unidade, uma vez que normalmente têm muito mais lógica de negócios.

Nesta seção, os testes de unidade validarão a saída da função do Orchestrator `E1_HelloSequence`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

O código de teste de unidade será iniciado com a criação de uma simulação:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Em seguida, as chamadas de método de atividade serão simuladas:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Em seguida, o teste de unidade chamará o método `HelloSequence.Run`:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

E, finalmente, a saída será validada:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Após a combinação de todas as etapas, o teste de unidade terá o código a seguir:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Testes de unidade das funções de atividade

As funções de atividade podem ter unidades testadas da mesma maneira que funções não duráveis.

Nesta seção, o teste de unidade validará o comportamento da função de Atividade `E1_SayHello`:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

E os testes de unidade verificarão o formato da saída. Os testes de unidade podem usar os tipos de parâmetro diretamente ou imitar a classe [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html):

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [Saiba mais sobre moq](https://github.com/Moq/moq4/wiki/Quickstart)
