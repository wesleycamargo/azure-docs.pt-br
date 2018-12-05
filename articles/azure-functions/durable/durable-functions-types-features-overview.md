---
title: Visão geral dos tipos de função e recursos para Funções Duráveis – Azure
description: Conheça os tipos de funções e as funções que permitem a comunicação entre funções como parte de uma orquestração de função durável.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/04/2018
ms.author: azfuncdf
ms.openlocfilehash: 265314ebf2568bd586934d371e1e6c1d74e0b9bb
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637011"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Visão geral dos tipos de função e recursos para Funções Duráveis (Azure Functions)

As Funções Duráveis do Azure fornecem orquestração com estado da execução da função. Uma função durável é uma solução composta por diferentes Azure Functions. Cada uma dessas funções pode ter diferentes funções como parte de uma orquestração. O documento a seguir fornece uma visão geral dos tipos de funções envolvidos em uma orquestração de função durável. Ele também inclui alguns padrões comuns na conexão das funções.  Para começar agora, crie sua primeira função durável em [ C# ](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md).

![Tipos de funções duráveis][1]  

## <a name="types-of-functions"></a>Tipos de funções

### <a name="activity-functions"></a>Funções de atividade

Funções de atividade são a unidade básica de trabalho em uma orquestração durável.  Funções de atividade são as funções e as tarefas que estão sendo orquestradas no processo.  Por exemplo, você pode criar uma função durável para processar uma ordem – verificar o inventário, cobrar o cliente e criar uma remessa.  Cada uma dessas tarefas é uma função de atividade.  As funções de atividade não têm nenhuma restrição no tipo de trabalho que você pode fazer nelas.  Elas podem ser escritas em qualquer linguagem compatível com o Azure Functions.  A estrutura de tarefa durável garante que cada função de atividade chamada seja executada pelo menos uma vez durante uma orquestração.

Uma função de atividade precisa ser disparada por um [gatilho de atividade](durable-functions-bindings.md#activity-triggers).  Essa função receberá um [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) como parâmetro. Você também pode associar o gatilho a qualquer outro objeto para passar entradas para a função.  A função de atividade também pode retornar valores para o orquestrador.  Se estiver enviando ou retornando vários valores de uma função de atividade, [utilize tuplas ou matrizes](durable-functions-bindings.md#passing-multiple-parameters).  As funções de atividade podem ser disparadas apenas em uma instância de orquestração.  Embora uma parte do código possa ser compartilhada entre uma função de atividade e outra função (como uma função disparada por HTTP), cada função pode ter apenas um gatilho.

Encontre mais informações e exemplos no [artigo sobre associação de Funções Duráveis](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Funções de orquestrador

As funções de orquestrador são o centro de uma função durável.  As funções de orquestrador descrevem a forma e a ordem na qual as ações são executadas.  As funções de orquestrador descrevem a orquestração no código (C# ou JavaScript), conforme mostrado na [Visão geral das funções duráveis](durable-functions-overview.md).  Uma orquestração pode ter muitos tipos diferentes de ações, como [atividade do Functions](#activity-functions), [suborquestrações](#sub-orchestrations), [espera de eventos externos](#external-events) e [temporizadores](#durable-timers).  

Uma função de orquestrador precisa ser disparada por um [gatilho de orquestração](durable-functions-bindings.md#orchestration-triggers).

Um orquestrador é iniciado por um [cliente orquestrador](#client-functions) que pode ele mesmo ser disparado de qualquer fonte (HTTP, filas, fluxos de eventos).  Cada instância de uma orquestração tem um identificador de instância, que pode ser gerado automaticamente (recomendado) ou gerado pelo usuário.  Esse identificador pode ser usado para [gerenciar instâncias](durable-functions-instance-management.md) da orquestração.

Encontre mais informações e exemplos no [artigo sobre associação de Funções Duráveis](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Funções do cliente

As funções do cliente são as funções disparadas que criarão instâncias de uma orquestração.  Elas são o ponto de entrada para a criação de uma instância de uma orquestração durável.  As funções do cliente podem ser disparadas por qualquer gatilho (HTTP, filas, fluxos de eventos, etc.) e escritas em qualquer linguagem compatível com o aplicativo.  Além do gatilho, as funções de cliente têm uma associação de [cliente de orquestração](durable-functions-bindings.md#orchestration-client) que lhes permite criar e gerenciar orquestrações duráveis.  O exemplo mais básico de uma função de cliente é uma função disparada por HTTP que inicia uma função de orquestrador e retorna uma resposta de status de verificação, conforme [mostrado no exemplo a seguir](durable-functions-http-api.md#http-api-url-discovery).

Encontre mais informações e exemplos no [artigo sobre associação de Funções Duráveis](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Recursos e padrões

### <a name="sub-orchestrations"></a>Subtipo de orquestrações

Além de chamar funções de atividade, as funções de orquestrador podem chamar outras funções de orquestrador. Por exemplo, você pode criar uma orquestração maior usando uma biblioteca de funções de orquestrador. Ou pode executar várias instâncias de uma função de orquestrador em paralelo.

Encontre mais informações e exemplos no [artigo sobre suborquestração](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Temporizadores duráveis

As [Funções Duráveis](durable-functions-overview.md) fornecem *temporizadores duráveis* para uso em funções de orquestrador para implementar atrasos ou para configurar tempos limite em ações assíncronas. Temporizadores duráveis devem ser usados em funções de orquestrador em vez de `Thread.Sleep` ou `Task.Delay` (C#) ou `setTimeout()` e `setInterval()` (JavaScript).

Encontre mais informações e exemplos de temporizadores duráveis no [artigo sobre temporizadores duráveis](durable-functions-timers.md)

### <a name="external-events"></a>Eventos externos

As funções de orquestrador podem aguardar os eventos externos atualizarem uma instância de orquestração. Frequentemente, esse recurso de Funções Duráveis é útil para lidar com a interação humana ou outros retornos de chamada externos.

Encontre mais informações e exemplos no [artigo sobre eventos externos](durable-functions-external-events.md).

### <a name="error-handling"></a>Tratamento de erros

As orquestrações de Funções Duráveis são implementadas no código e podem usar os recursos de tratamento de erro da linguagem de programação.  Isso significa que padrões como "try/catch" funcionarão na orquestração.  As funções duráveis também são fornecidas com algumas políticas de repetição internas.  Uma ação pode atrasar e repetir as atividades automaticamente em caso de exceções.  As repetições permitem tratar exceções transitórias, sem a necessidade de abandonar a orquestração.

Encontre mais informações e exemplos no [artigo sobre tratamento de erro](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Comunicação do aplicativo entre funções

Embora uma orquestração durável geralmente resida em um contexto de um único aplicativo de funções, há padrões para permitir que você coordene as orquestrações em muitos aplicativos de funções.  Mesmo que a comunicação entre aplicativos possa estar ocorrendo por HTTP, o uso da estrutura durável para cada atividade significa que você ainda pode manter um processo durável entre dois aplicativos.

Um exemplo de uma orquestração de aplicativo de função cruzada no C# é fornecido abaixo.  Uma atividade iniciará a orquestração externa. Em seguida, outra atividade recuperará e retornará o status.  O orquestrador aguardará a conclusão do status antes de continuar.

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Continuar lendo a documentação das Funções Duráveis](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
