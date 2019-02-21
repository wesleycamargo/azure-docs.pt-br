---
title: padrões e conceitos técnicos sobre as Durable Functions
description: Fornece detalhes sobre o funcionamento das Durable Functions no Azure para habilitar os benefícios da execução do código de estado na nuvem.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 6eb08af9cdd19bc83d44d29874f6ac58b41ed8c8
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56302023"
---
# <a name="durable-functions-patterns-and-technical-concepts"></a>Padrões e conceitos técnicos sobre as Durable Functions

As *Funções Duráveis* são uma extensão do [Azure Functions](../functions-overview.md) e do [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você. Este artigo fornece informações mais detalhadas sobre os comportamentos da extensão Durable Functions para o Azure Functions e padrões comuns de implementação.

> [!NOTE]
> As Funções Duráveis são uma extensão avançada do Azure Functions que não é apropriada para todos os aplicativos. O restante deste artigo pressupõe que você tenha uma forte familiaridade com os conceitos do [Azure Functions](../functions-overview.md) e com os desafios envolvidos no desenvolvimento de aplicativos serverless.

## <a name="patterns"></a>Padrões

Estas seção descreve alguns padrões de aplicativo típicos que podem se beneficiar das Durable Functions.

### <a name="chaining"></a>Padrão 1: Encadeamento de funções

*Encadeamento de funções* é o padrão de executar uma sequência de funções em uma ordem específica. Frequentemente, a saída de uma função precisa ser aplicada à entrada de outra função.

![Diagrama de encadeamento de funções](./media/durable-functions-concepts/function-chaining.png)

As Funções Duráveis permitem implementar esse padrão de forma concisa no código.

#### <a name="c-script"></a>Script C#

```cs
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

> [!NOTE]
> Há diferenças sutis durante a gravação de uma função durável pré-compilada no C# vs o exemplo de script C# mostrado anteriormente. Uma função C# pré-compilada exigiria que parâmetros duráveis fossem decorados com os respectivos atributos. Um exemplo é o `[OrchestrationTrigger]` atributo o `DurableOrchestrationContext` parâmetro. Se os parâmetros não são decorados adequadamente, o tempo de execução não será capaz de injetar as variáveis para a função e resultará em erro. Visite [exemplo](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples) para obter mais exemplos.

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Os valores de "F1", "F2", "F3" e "F4" são os nomes de outras funções no aplicativo de funções. O fluxo de controle é implementado usando construtos de codificação imperativa normal. Ou seja, o código é executado de cima para baixo e pode envolver a semântica do fluxo de controle da linguagem existente, como condicionais e loops.  A lógica de tratamento de erro pode ser incluída em blocos try/catch/finally.

O parâmetro `context` [DurableOrchestrationContext] \(.NET\) e o objeto `context.df` (JavaScript) fornecem métodos para chamar outras funções por nome, passar parâmetros e retornar a saída da função. Sempre que o código chama `await` (C#) ou `yield` (JavaScript), a estrutura das Durable Functions faz a *verificação pontual* do progresso da instância da função atual. Se o processo ou a VM for reciclada no meio da execução, a instância da função continuará na chamada `await` ou `yield` anterior. Mais informações sobre esse comportamento de reinicialização são fornecidas mais adiante.

> [!NOTE]
> O objeto `context` no JavaScript representa o [contexto de função](../functions-reference-node.md#context-object) como um todo, não o [DurableOrchestrationContext].

### <a name="fan-in-out"></a>Padrão 2: Fan-out/fan-in

*Fan-out/fan-in* é o padrão de executar várias funções em paralelo e, em seguida, esperar que todas sejam concluídas.  Frequentemente, algum trabalho de agregação é feito nos resultados retornados pelas funções.

![Diagrama de fan-out/fan-in](./media/durable-functions-concepts/fan-out-fan-in.png)

Com funções normais, o fan-out pode ser feito fazendo com que a função envie várias mensagens para uma fila. No entanto, o processo de fazer fan-in é muito mais desafiador. Você precisaria escrever um código para controlar quando as funções disparadas pela fila terminam e armazenar as saídas da função. A extensão de Funções Duráveis lida com esse padrão com um código relativamente simples.

#### <a name="c-script"></a>Script C#

```cs
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // get a list of N work items to process in parallel
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

O trabalho de fan-out é distribuído para várias instâncias da função `F2` e o trabalho é controlado usando uma lista dinâmica de tarefas. A API `Task.WhenAll` .NET ou `context.df.Task.all` JavaScript é chamada para aguardar até que todas as funções chamadas sejam concluídas. Em seguida, as saídas da função `F2` são agregadas da lista de tarefas dinâmicas e passadas para a função `F3`.

A verificação pontual automática que ocorre na chamada `await` ou `yield` em `Task.WhenAll` ou `context.df.Task.all` garante que qualquer falha ou reinicialização no meio do processo não exija uma reinicialização de tarefas já concluídas.

### <a name="async-http"></a>Padrão 3: APIs HTTP assíncronas

O terceiro padrão trata do problema de coordenar o estado de operações de longa execução com clientes externos. Uma maneira comum de implementar esse padrão é fazer com que a ação de longa execução seja disparada por uma chamada HTTP e, em seguida, redirecionar o cliente para um ponto de extremidade de status que ele possa sondar para saber quando a operação for concluída.

![Diagrama de API HTTP](./media/durable-functions-concepts/async-http-api.png)

As Funções Duráveis fornecem APIs internas que simplificam o código que você escreve para interagir com execuções de função de longa execução. Os exemplos de início rápido ([C#](durable-functions-create-first-csharp.md), [JavaScript](quickstart-js-vscode.md)) mostram um comando REST simples que pode ser usado para iniciar novas instâncias de função do orquestrador. Depois que uma instância é iniciada, a extensão expõe as APIs HTTP de webhook que consultam o status da função orquestradora. O exemplo a seguir mostra os comandos REST para iniciar um orquestrador e para consultar seu status. Para maior clareza, alguns detalhes foram omitidos do exemplo.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Como o estado é gerenciado pelo tempo de execução das Funções Duráveis, você não precisa implementar seu próprio mecanismo de controle de status.

Embora a extensão de Funções Duráveis tenha webhooks internos para gerenciar orquestrações de longa execução, você mesmo pode implementar esse padrão usando seus próprios gatilhos de função (como HTTP, fila ou Hub de Eventos) e a associação `orchestrationClient`. Por exemplo, você pode usar uma mensagem da fila para disparar o encerramento.  Ou você pode usar um gatilho HTTP protegido por uma política de autenticação do Azure Active Directory, em vez de webhooks internos que usam uma chave gerada para autenticação.

#### <a name="c"></a>C#

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
// HTTP-triggered function to start a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // Function name comes from the request URL.
    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

> [!WARNING]
> Ao fazer o desenvolvimento local em JavaScript, você precisará definir a variável de ambiente `WEBSITE_HOSTNAME` como `localhost:<port>`, por exemplo, `localhost:7071` para usar métodos em `DurableOrchestrationClient`. Para obter mais informações sobre esse requisito, confira o [problema no GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

Em .NET, o parâmetro de [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` é um valor da associação de saída `orchestrationClient`, que faz parte da extensão de Durable Functions. Em JavaScript, esse objeto é retornado ao chamar `df.getClient(context)`. Esses objetos fornecem métodos para iniciar, enviar eventos, encerrar e consultar instâncias novas ou existentes da função orquestradora.

No exemplo anterior, uma função disparada por HTTP assume um valor de `functionName` da URL de entrada e passa esse valor para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Essa API de associação [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) então retorna uma resposta que contém um cabeçalho `Location` e informações adicionais sobre a instância, que posteriormente podem ser usadas para pesquisar o status da instância iniciada ou encerrá-la.

### <a name="monitoring"></a>Padrão 4: Monitoramento

O padrão de monitoramento refere-se a um processo *recorrente* flexível em um fluxo de trabalho - por exemplo, sondagem até que determinadas condições sejam atendidas. Um [gatilho com timer](../functions-bindings-timer.md) normal pode lidar com um cenário simples, como um trabalho de limpeza periódico, mas seu intervalo é estático e o gerenciamento do tempo de vida da instância torna-se complexo. As Funções Duráveis permitem intervalos de recorrência flexíveis, gerenciamento de tempo de vida da tarefa e a capacidade de criar vários processos de monitoramento a partir de uma única orquestração.

Um exemplo seria reverter o cenário anterior da API HTTP assíncrona. Em vez de expor um ponto de extremidade para um cliente externo monitorar uma operação de execução longa, o monitor de execução longa consome um ponto de extremidade externo, aguardando alguma alteração de estado.

![Diagrama de monitoramento](./media/durable-functions-concepts/monitor.png)

Usando Funções Duráveis, vários monitores que observam pontos de extremidade arbitrários podem ser criados em poucas linhas de código. Os monitores podem encerrar a execução quando alguma condição for atendida ou serem terminados pelo [DurableOrchestrationClient](durable-functions-instance-management.md) e o intervalo de espera pode ser alterado com base em alguma condição (por exemplo, retirada exponencial.) O código a seguir implementa um monitor básico.

#### <a name="c-script"></a>Script C#

```cs
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform action when condition met
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

Quando uma solicitação é recebida, uma nova instância de orquestração é criada para essa ID do trabalho. A instância sonda um status até que uma condição seja atendida e o loop seja encerrado. Um temporizador durável é usado para controlar o intervalo de sondagem. Trabalho adicional pode ser realizado, ou a orquestração pode encerrar. Quando o `context.CurrentUtcDateTime` (.NET) ou o `context.df.currentUtcDateTime` (JavaScript) excede o `expiryTime`, o monitor é encerrado.

### <a name="human"></a>Padrão 5: Interação humana

Muitos processos envolvem algum tipo de interação humana. O aspecto complicado de envolver pessoas em um processo automatizado é que as pessoas nem sempre são tão disponíveis e ágeis como os serviços de nuvem. Processos automatizados devem levar isso em conta e eles normalmente fazem isso usando a lógica de tempos limite e compensação.

Um exemplo de um processo de negócios que envolve a interação humana é um processo de aprovação. Por exemplo, a aprovação de um gerente pode ser necessária para um relatório de despesas que ultrapasse um determinado valor. Se o gerente não aprová-lo dentro de 72 horas (talvez ele esteja de férias), um processo de escalonamento é acionado para obter a aprovação de outra pessoa (talvez o gerente do gerente).

![Diagrama de interação humana](./media/durable-functions-concepts/approval.png)

Esse padrão pode ser implementado usando uma função de orquestrador. O orquestrador usaria um [temporizador durável](durable-functions-timers.md) para solicitar a aprovação e escalar em caso de tempo limite. Ele esperaria por um [evento externo](durable-functions-external-events.md), que seria a notificação gerada por alguma interação humana.

#### <a name="c-script"></a>Script C#

```cs
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```js
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

O temporizador durável é criado chamando `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript). A notificação é recebida pelo `context.WaitForExternalEvent` (.NET) ou `context.df.waitForExternalEvent` (JavaScript). E `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript) é chamado para decidir se o próximo passo é escalonar (o tempo limite acontece primeiro) ou processar a aprovação (aprovação recebida antes do tempo limite).

Um cliente externo pode entregar a notificação de eventos para uma função do orchestrator em espera usando o [APIs de HTTP interna](durable-functions-http-api.md#raise-event) ou usando a API [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) a partir de outra função:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="the-technology"></a>A tecnologia

Nos cenários, a extensão Funções Duráveis é compilada no [Framework de Tarefa Durável](https://github.com/Azure/durabletask), uma biblioteca de software livre no GitHub para compilar orquestrações de tarefas duráveis. Assim como o Azure Functions é a evolução sem servidor do Azure WebJobs, as Funções Duráveis são a evolução sem servidor do Framework de Tarefa Durável. O Framework de Tarefa Durável é muito usado dentro e fora da Microsoft para automatizar processos essenciais. Ele é uma opção natural para o ambiente sem servidor do Azure Functions.

### <a name="event-sourcing-checkpointing-and-replay"></a>Fornecimento de eventos, ponto de verificação e reprodução

As funções orquestradoras mantêm seu estado de execução, de forma confiável, usando um padrão de design conhecido como [Fornecimento de Eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Em vez de armazenar diretamente o estado *atual* de uma orquestração, a extensão durável usa um armazenamento somente de acréscimo para registrar a *série completa de ações* realizadas pela orquestração de função. Isso traz muitos benefícios, incluindo a melhorar o desempenho, a escalabilidade e a capacidade de resposta, em comparação com "despejar" todo o estado de tempo de execução. Outros benefícios incluem fornecer consistência eventual para dados transacionais e manter históricos e trilhas de auditoria completas. As próprias trilhas de auditoria habilitam ações de compensação confiáveis.

O uso do Fornecimento de eventos por esta extensão é transparente. Nos bastidores, o operador `await` (C#) ou `yield` (JavaScript) de uma função orquestradora leva o controle do thread do orquestrador de volta para o dispatcher do Framework de Tarefa Durável. O dispatcher, em seguida, confirma novas ações agendadas pela função orquestradora (como chamar uma ou mais funções filho ou agendar um temporizador durável) no armazenamento. Essa ação de confirmação transparente é acrescentada ao *histórico de execução* da instância de orquestração. O histórico é armazenado na tabela de armazenamento. A ação de confirmação, em seguida, adiciona mensagens a uma fila para agendar o trabalho de fato. Neste ponto, a função de orquestrador pode ser descarregada da memória. A cobrança por ela para se você estiver usando o Plano Consumo do Azure Functions.  Quando há mais trabalho a ser feito, a função é reiniciada e seu estado é reconstruído.

Depois que uma função de orquestração recebe mais trabalho a fazer (por exemplo, uma mensagem de resposta é recebida ou um temporizador durável expira), o orquestrador é ativado novamente e executa mais uma vez a função inteira, desde o início, para reconstruir o estado local. Se, durante essa reprodução, o código tentar chamar uma função (ou qualquer outro tipo de trabalho assíncrono), o Framework de Tarefa Durável consultará o *histórico de execução* da orquestração atual. Se ele detectar que a [função de atividade](durable-functions-types-features-overview.md#activity-functions) já foi executada e gerou alguns resultados, ele reproduzirá o resultado da função e o código do orquestrador continuará em execução. Isso continuará acontecendo até que o código da função chegue a um ponto em que seja concluído ou tenha um novo trabalho assíncrono agendado.

### <a name="orchestrator-code-constraints"></a>Restrições de código do orquestrador

O comportamento de reprodução cria restrições quanto ao tipo do código que pode ser escrito em uma função de orquestrador. Por exemplo, código de orquestrador deve ser determinístico, uma vez que ele será reproduzido várias vezes e deve produzir o mesmo resultado toda vez. A lista completa de restrições pode ser encontrada na seção [Restrições de código do orquestrador](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) no artigo **Ponto de verificação e reinicialização**.

## <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico

A extensão de Funções Duráveis emite automaticamente dados de acompanhamento estruturados para o [Application Insights](../functions-monitoring.md) quando o aplicativo de funções é configurado com uma chave de instrumentação do Application Insights. Dados de acompanhamento podem ser usados para monitorar o comportamento e o progresso de suas orquestrações.

Veja um exemplo dos eventos de acompanhamento das Funções Duráveis no portal do Application Insights usando o [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Resultados de consulta do Application Insights](./media/durable-functions-concepts/app-insights-1.png)

Há muito dados estruturados úteis empacotados no campo `customDimensions` em cada entrada de log. Este é um exemplo de uma entrada desse tipo totalmente expandida.

![Campo customDimensions na consulta do Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Devido ao comportamento de reprodução do dispatcher do Framework de Tarefa Durável, você pode esperar ver entradas de log redundantes para ações reproduzidas. Isso pode ser útil para entender o comportamento de reprodução do mecanismo central. O artigo [Diagnóstico](durable-functions-diagnostics.md) mostra exemplos de consultas que filtram logs de reprodução para que você possa ver apenas os logs "em tempo real".

## <a name="storage-and-scalability"></a>Armazenamento e a escalabilidade

A extensão de Funções Duráveis usa blobs, tabelas e filas de Armazenamento do Azure para persistir o estado do histórico de execução e disparar a execução da função. A conta de armazenamento padrão do aplicativo de funções pode ser usada ou você pode configurar uma conta de armazenamento separada. Talvez você queira uma conta separada devido aos limites de taxa de transferência de armazenamento. O código do orquestrador que você escrever não precisa (e não deve) interagir com as entidades nessas contas de armazenamento. As entidades são gerenciadas diretamente pelo Framework de Tarefa Durável como um detalhe de implementação.

As funções de orquestrador agendam funções de atividade e recebem suas respostas por meio de mensagens de fila interna. Quando um aplicativo de funções é executado no Plano de Consumo do Azure Functions, essas filas são monitoradas pelo [Controlador de escala do Azure Functions](../functions-scale.md#how-the-consumption-plan-works) e novas instâncias de computação são adicionadas conforme necessário. Quando expandida para várias VMs, uma função de orquestrador pode ser executada em uma VM enquanto as funções de atividade chamadas por ele são executadas em várias VMs diferentes. Você pode encontrar mais detalhes sobre o comportamento de escala das Funções Duráveis em [Desempenho e escalabilidade](durable-functions-perf-and-scale.md).

O armazenamento de tabela é usado para armazenar o histórico de execução das contas do orquestrador. Sempre que uma instância for reidratada em uma VM específica, ela buscará seu histórico de execução do armazenamento de tabelas para que possa recriar seu estado local. Um dos aspectos convenientes de ter o histórico disponível no armazenamento de tabelas é que você pode dar uma olhada para ver o histórico de sua orquestrações usando ferramentas como o [Gerenciador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Os blobs de armazenamento são usados principalmente como um mecanismo de concessão para coordenar a expansão de instâncias de orquestração em várias VMs. Também são usados para armazenar dados de mensagens grandes que não podem ser armazenados diretamente em tabelas ou filas.

![Captura de tela do Gerenciador de Armazenamento do Azure](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> Embora seja fácil e conveniente ver o histórico de execução no armazenamento de tabela, evite depender desta tabela. Ela pode mudar à medida que a extensão de Funções Duráveis evoluir.

## <a name="known-issues-and-faq"></a>Problemas conhecidos e perguntas frequentes

Todos os problemas conhecidos devem estar registrados na lista de [Problemas no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Se você tiver um problema e não for possível localizá-lo no GitHub, abra um novo problema e inclua uma descrição detalhada dele.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as Durable Functions, consulte [Visão geral dos tipos de função e recursos para Funções Duráveis (Azure Functions)](durable-functions-types-features-overview.md) ou ...

> [!div class="nextstepaction"]
> [Crie sua primeira função durável](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html