---
title: Diagnóstico nas Funções Duráveis – Azure
description: Saiba como diagnosticar problemas com a extensão de Funções Duráveis do Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: ce76f36b93d6dabd83fbaa93357c2dc96209a1c0
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087583"
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnóstico nas Funções Duráveis (Azure Functions)

Há várias opções para diagnosticar problemas com as [Funções Duráveis](durable-functions-overview.md). Algumas dessas opções são as mesmas usadas para funções regulares e algumas delas são exclusivas das Funções Duráveis.

## <a name="application-insights"></a>Application Insights

O [Application Insights](../application-insights/app-insights-overview.md) é a maneira recomendada de fazer diagnóstico e monitoramento no Azure Functions. O mesmo se aplica às Funções Duráveis. Para obter uma visão geral de como usar o Application Insights em seu aplicativo de funções, consulte [Monitor o Azure Functions](functions-monitoring.md).

A Extensão Durável do Azure Functions também emite *eventos de acompanhamento*, que permitem rastrear a execução de uma orquestração de ponta a ponta. Eles podem ser encontrados e consultados usando a ferramenta [Application Insights Analytics](../application-insights/app-insights-analytics.md) no portal do Azure.

### <a name="tracking-data"></a>Acompanhamento de dados

Cada evento de ciclo de vida de uma instância de orquestração faz com que um evento de acompanhamento seja gravado na coleção de **rastreamentos** no Application Insights. Esse evento tem um conteúdo **customDimensions** com vários campos.  Todos os nomes de campo são prefixados com `prop__`.

* **hubName**: o nome do hub de tarefas no qual suas orquestrações estão em execução.
* **appName**: o nome do aplicativo de funções. Ele é útil quando você tem vários aplicativos de funções compartilhando a mesma instância do Application Insights.
* **slotName**: o [slot de implantação](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) no qual o aplicativo de funções atual está sendo executado. Ele é útil quando você aproveitar os slots de implantação para controlar a versão de suas orquestrações.
* **functionName**: o nome da função de orquestrador ou atividade.
* **functionType**: o tipo da função, como **Orquestrador** ou **Atividade**.
* **instanceId**: a ID exclusiva da instância de orquestração.
* **state**: o estado de execução do ciclo de vida da instância. Os valores válidos incluem:
    * **Scheduled**: a função foi agendada para execução, mas ainda não começou a ser executada.
    * **Started**: a função começou a ser executada, mas não ainda esperou ou foi concluída.
    * **Awaited**: o orquestrador agendou algum trabalho e está aguardando sua conclusão.
    * **Listening**: o orquestrador está escutando uma notificação de evento externo.
    * **Completed**: a função foi concluída com êxito.
    * **Failed**: a função falhou com um erro.
* **reason**: dados adicionais associados ao evento de acompanhamento. Por exemplo, se uma instância estiver aguardando uma notificação de evento externo, esse campo indica o nome do evento que ela está aguardando. Se uma função tiver falhado, ele conterá detalhes do erro.
* **isReplay**: valor booliano que indica se o evento de acompanhamento deve ter a execução reproduzida.
* **extensionVersion**: a versão da extensão da Tarefa Durável. Esse dado é especialmente importante ao relatar possíveis bugs na extensão. Instâncias de execução longa podem relatar várias versões se uma atualização ocorrer durante sua execução. 
* **sequenceNumber**: número de sequência de execução para um evento. Combinado com o carimbo de data/hora ajuda a ordenar os eventos por tempo de execução. *Observe que esse número será redefinido para zero se o host for reiniciado enquanto a instância estiver em execução, portanto, primeiro é importante sempre classificar pelo carimbo de data/hora e, depois, sequenceNumber.*

O detalhamento dos dados de acompanhamento emitidos para o Application Insights pode ser configurado na seção `logger` do arquivo `host.json`.

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

Por padrão, todos os eventos de acompanhamento de não-replay são emitidos. O volume dos dados pode ser reduzido definindo `Host.Triggers.DurableTask` como `"Warning"` ou `"Error"`. Nesse caso, os eventos de acompanhamento serão emitidos somente para situações excepcionais.

Para habilitar a emitir os eventos de reprodução de orquestração detalhado, o `LogReplayEvents` pode ser definido como `true` na `host.json` do arquivo sob `durableTask` conforme mostrado:

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

> [!NOTE]
> Por padrão, a amostragem da telemetria do Application Insights é feita segundo o tempo de execução do Azure Functions, para evitar a emissão de dados com frequência excessiva. Isso pode fazer com que informações de acompanhamento sejam perdidas quando muitos eventos de ciclo de vida ocorrerem em um curto período. O [artigo sobre Monitoramento no Azure Functions](functions-monitoring.md#configure-sampling) explica como configurar esse comportamento.

### <a name="single-instance-query"></a>Consulta de instância única

A consulta a seguir mostra dados de acompanhamento históricos de uma única instância de orquestração da função [Sequência Hello](durable-functions-sequence.md). Ela é escrita usando a [AIQL (Linguagem de Consulta do Application Insights)](https://aka.ms/LogAnalyticsLanguageReference). Ela filtra a execução de reproduções para que somente o caminho de execução *lógico* seja mostrado. Os eventos podem ser ordenados classificando por `timestamp` e `sequenceNumber`, conforme mostrado na consulta abaixo: 

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"]) 
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

O resultado é uma lista de eventos de rastreamento que mostra o caminho de execução da orquestração, incluindo quaisquer funções de atividade ordenadas pelo tempo de execução em ordem crescente.

![Consulta do Application Insights](media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)


### <a name="instance-summary-query"></a>Consulta de resumo da instância

A consulta a seguir exibe o status de todas as instâncias de orquestração que foram executadas em um intervalo de tempo especificado.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
O resultado é uma lista de IDs de instância e seu status de tempo de execução atual.

![Consulta do Application Insights](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Registro em log

É importante ter em mente o comportamento de reprodução do orquestrador ao gravar logs diretamente de uma função de orquestrador. Por exemplo, considere a seguinte função de orquestrador:

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Os dados de log resultantes serão semelhantes ao seguinte:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Lembre-se de que, embora os logs declarem estar chamando F1, F2 e F3, essas funções *realmente* são chamadas apenas na primeira vez em que são encontradas. Chamadas posteriores que ocorrem durante a reprodução são ignoradas e as saídas são reproduzidas para a lógica do orquestrador.

Se quiser registrar no log apenas a execução sem reproduções, você poderá escrever uma expressão condicional para registrar apenas se `IsReplaying` for `false`. Considere o exemplo anterior, mas desta vez com verificações de reprodução.

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    ILogger log)
{
    if (!ctx.IsReplaying) log.LogInformation("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.LogInformation("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.LogInformation("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (apenas Functions v2)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Com essa alteração, a saída do log será a seguinte:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Status personalizados

O status de orquestração personalizado permite que você defina um valor de status personalizado para a função do orquestrador. Esse status é fornecido por meio da API de consulta de status HTTP ou da API `DurableOrchestrationClient.GetStatusAsync`. O status de orquestração personalizado possibilita um monitoramento mais rico para funções do orquestrador. Por exemplo, o código de função do orquestrador pode incluir `DurableOrchestrationContext.SetCustomStatus` chamadas para atualizar o progresso de uma operação demorada. Um cliente, como uma página da web ou outro sistema externo pode, em seguida, consultar periodicamente as APIs de consulta de status HTTP para informações de andamento mais ricas. Um exemplo usando `DurableOrchestrationContext.SetCustomStatus` é fornecido abaixo:

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> O status de orquestração personalizado para JavaScript estará disponível em uma versão futura.

Durante a execução da orquestração, clientes externos podem buscar este status personalizado:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

O clientes terão a seguinte resposta: 

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
>  O conteúdo do status personalizado é limitado a 16 KB de textoUTF-16 JSON porque ele precisa ser capaz de caber em uma coluna de Armazenamento de Tabelas do Azure. Você pode usar o armazenamento externo se precisar de conteúdo maior.

## <a name="debugging"></a>Depurando

O Azure Functions dá suporte à depuração do código de função diretamente e esse mesmo suporte se estende às Funções Duráveis, seja em execução no Azure ou localmente. No entanto, há alguns comportamentos a que você deve estar atento ao depurar:

* **Reprodução**: funções de orquestrador são reproduzidas regularmente quando novas entradas são recebidas. Isso significa que uma única execução *lógica* de uma função de orquestrador pode atingir o mesmo ponto de interrupção várias vezes, especialmente se ele estiver definido no início do código da função.
* **Aguardar**: sempre que um `await` é encontrado, ele leva o controle de volta para o dispatcher do Framework de Tarefa Durável. Se esta for a primeira vez que um determinado `await` foi encontrado, a tarefa associada *nunca* será retomada. Uma vez que a tarefa nunca é retomada, *ignorar* a espera (F10 no Visual Studio) não é possível. Ignorar só funciona quando uma tarefa está sendo reproduzida.
* **Tempos limite de mensagem**: as Funções Duráveis usam internamente mensagens de fila para acionar a execução de funções de orquestrador e de funções de atividade. Em um ambiente com várias VMs, interromper a depuração por longos períodos pode fazer com que outra VM receba a mensagem, resultando em uma execução duplicada. Esse comportamento também existe para funções de gatilho de fila regulares, mas é importante ressaltar neste contexto, uma vez que as filas são um detalhe de implementação.

> [!TIP]
> Ao definir pontos de interrupção, se quiser interromper somente a execução que não é de repetição, você pode definir um ponto de interrupção condicional que interrompe somente se `IsReplaying` for `false`.

## <a name="storage"></a>Armazenamento

Por padrão, as Funções Duráveis armazenam o estado no Armazenamento do Azure. Isso significa que você pode inspecionar o estado de suas orquestrações usando ferramentas como o [Gerenciador de Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de tela do Gerenciador de Armazenamento do Azure](media/durable-functions-diagnostics/storage-explorer.png)

Isso é útil para a depuração, pois você vê exatamente em qual estado uma orquestração pode estar. Mensagens nas filas também podem ser examinadas para saber qual trabalho está pendente (ou preso, em alguns casos).

> [!WARNING]
> Embora seja conveniente ver o histórico de execução no armazenamento de tabelas, evite depender desta tabela. Ela pode mudar à medida que a extensão de Funções Duráveis evoluir.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar temporizadores duráveis](durable-functions-timers.md)
