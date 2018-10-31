---
title: Gerenciar instâncias nas Funções Duráveis – Azure
description: Saiba como gerenciar instâncias na extensão de Funções Duráveis do Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: azfuncdf
ms.openlocfilehash: c9b3cd112cef7a34e0d475cdeb85b9e07d77f584
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352586"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Gerenciar instâncias nas Funções Duráveis (Azure Functions)

As instâncias de orquestração das [Funções Duráveis](durable-functions-overview.md) podem ser iniciadas, encerradas, consultadas e podem receber eventos de notificação. Todo o gerenciamento de instâncias é feito usando a [associação de cliente de orquestração](durable-functions-bindings.md). Este artigo mostra os detalhes de cada operação de gerenciamento de instância.

## <a name="starting-instances"></a>Iniciar instâncias

O método [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) no [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) inicia uma nova instância de uma função de orquestrador. Instâncias dessa classe podem ser obtidas usando a associação `orchestrationClient`. Internamente, esse método enfileira uma mensagem na fila de controle, que por sua vez dispara o início de uma função com o nome especificado que usa a associação de gatilho `orchestrationTrigger`. 

A tarefa é concluída quando o processo de orquestração é iniciado. O processo de orquestração deve começar dentro de 30 segundos. Se demorar mais, um `TimeoutException` é gerado. 

Os parâmetros para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) são os seguintes:

* **Name**: o nome da função de orquestrador a ser agendada.
* **Input**: Qualquer dado serializável em JSON, que deve ser passado como a entrada para a função de orquestrador.
* **InstanceId**: (opcional) a ID exclusiva da instância. Se não for especificada, uma ID de instância aleatória será gerada.

Veja um exemplo simples em C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

Para as linguagens diferentes de .NET, a associação de saída da função também pode ser usada para iniciar novas instâncias. Nesse caso, qualquer objeto serializável em JSON que tem os três parâmetros acima como campos pode ser usado. Por exemplo, considere a seguinte função JavaScript:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```
O código acima pressupõe que, no arquivo function.json, você definiu uma associação de saída com o nome "starter" e a digitou como "orchestrationClient". Se a associação não estiver definida, a instância de função durável não será criada.

Para a função durável ser invocada, function.json deve ser modificado para ter uma associação para o cliente de orquestração conforme descrito abaixo

```js
{
    "bindings": [{
        "name":"starter",
        "type":"orchestrationClient",
        "direction":"out"
    }]
}
```

> [!NOTE]
> Recomendamos que você use um identificador aleatório para a ID de instância. Isso ajudará a garantir uma distribuição de carga igual ao dimensionar funções de orquestrador entre várias VMs. O momento adequado para usar IDs de instância não aleatórias é quando a ID precisar ser proveniente de uma fonte externa ou ao implementar o padrão de [orquestrador singleton](durable-functions-singletons.md).

## <a name="querying-instances"></a>Consultar instâncias

O método [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) na classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) consulta o status de uma instância de orquestração. Ele usa um `instanceId` (obrigatório), `showHistory` (opcional) e `showHistoryOutput` (opcional) como parâmetros. Se `showHistory` é definido como `true`, a resposta conterá o histórico de execução. Se `showHistoryOutput` é definido como `true`, o histórico de execução conterá saídas da atividade. O método retorna um objeto com as seguintes propriedades:

* **Name**: o nome da função de orquestrador.
* **InstanceId**: a ID da instância de orquestração (deve ser o mesmo que a entrada `instanceId`).
* **CreatedTime**: a hora em que a função de orquestrador começou a ser executada.
* **LastUpdatedTime**: a hora em que a orquestração passou por uma verificação pontual pela última vez.
* **Input**: a entrada da função como um valor JSON.
* **CustomStatus**: status de orquestração personalizado no formato JSON. 
* **Output**: a saída da função como um valor JSON (se a função tiver sido concluída). Se a função de orquestrador tiver falhado, essa propriedade incluirá os detalhes da falha. Se a função de orquestrador tiver sido encerrada, essa propriedade incluirá o motivo fornecido para o encerramento (se houver).
* **RuntimeStatus**: um dos seguintes valores:
    * **Pendente**: A instância foi agendada, mas ainda não foi iniciado em execução.
    * **Running**: a instância começou a ser executada.
    * **Completed**: a instância foi concluída normalmente.
    * **ContinuedAsNew**: a instância reiniciou a si mesma com um novo histórico. Esse estado é temporário.
    * **Failed**: a instância falhou com um erro.
    * **Terminated**: a instância foi encerrada abruptamente.
* **Histórico**: O histórico de execução de orquestração. Este campo é preenchido somente se `showHistory` é definido como `true`.
    
Este método retornará `null` se a instância não existir ou não tiver começado a ser executada.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```
## <a name="querying-all-instances"></a>Consultar todas as instâncias

É possível usar o método `GetStatusAsync` para consultar os status de todas as instâncias de orquestração. Ele não usa nenhum parâmetro, ou você pode passar um objeto `CancellationToken` caso queira cancelar. O método retorna objetos com as mesmas propriedades que o método `GetStatusAsync` com parâmetros, exceto que ele não retorna o histórico. 

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    TraceWriter log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.Info(JsonConvert.SerializeObject(instance));
    };
}
```
## <a name="querying-instances-with-filters"></a>Consultar instâncias com filtros

Você também pode usar o método `GetStatusAsync` para obter uma lista de instâncias de orquestração que correspondem a um conjunto de filtros predefinidos. Opções de filtro possíveis incluem a hora de criação de orquestração e o status do tempo de execução da orquestração.

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    TraceWriter log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.Info(JsonConvert.SerializeObject(instance));
    };
}
```

## <a name="terminating-instances"></a>Encerrar instâncias

Uma instância de orquestração em execução pode ser encerrada usando o método [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Os dois parâmetros são um `instanceId` e uma cadeia de caracteres `reason`, que serão gravados em logs no status da instância. Uma instância encerrada deixará de ser executada assim que atingir o próximo ponto `await` ou será encerrada imediatamente se já estiver em um `await`. 

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> O encerramento de instância não propaga no momento. As funções de atividade e as suborquestrações serão executadas por completo, independentemente de a instância de orquestração que as chamou ter sido encerrada.

## <a name="sending-events-to-instances"></a>Enviar eventos para instâncias

Notificações de eventos podem ser enviadas a instâncias em execução usando o método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Instâncias que podem lidar com esses eventos são aquelas que estão aguardando uma chamada para [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

Os parâmetros para [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) são os seguintes:

* **InstanceId**: a ID exclusiva da instância.
* **EventName**: o nome do evento a ser enviado.
* **EventData**: uma carga serializável em JSON para enviar para a instância.

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!WARNING]
> Se não houver nenhuma instância de orquestração com a *ID de instância* especificada ou se a instância não estiver aguardando o *nome do evento* especificado, a mensagem de evento será descartada. Para obter mais informações sobre esse comportamento, consulte o [Problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Aguardar a conclusão da orquestração

A classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expõe uma API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) que pode ser usada para obter sincronicamente a saída real de uma instância de orquestração. O método usa o valor padrão de 10 segundos para `timeout` e 1 segundo para `retryInterval` quando eles não estão definidos.  

Veja um exemplo de função de gatilho HTTP que demonstra como usar essa API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

A função pode ser chamada com a seguinte linha usando o tempo limite de 2 segundos e o intervalo de repetição de 0,5 segundo:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, há dois casos:

1. As instâncias de orquestração são concluídas dentro do tempo limite definido (neste caso, 2 segundos), a resposta é a saída de instância real de orquestração entregue sincronicamente:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. As instâncias de orquestração não podem ser concluídas dentro do tempo limite definido (neste caso, 2 segundos), a resposta é o padrão descrito em **descoberta de URL HTTP da API**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> O formato das URLs de webhook pode variar dependendo de qual versão do host do Azure Functions você está executando. O exemplo acima refere-se ao host do Azure Functions 2.0.

## <a name="retrieving-http-management-webhook-urls"></a>Recuperar URLs do WebHok de gerenciamento de HTTP

Os sistemas externos podem se comunicar com as Funções Duráveis por meio das URLs do webhook que fazem parte da resposta padrão descrita na [descoberta de URL na API HTTP](durable-functions-http-api.md). No entanto, as URLs do webhook também podem ser acessadas programaticamente no cliente de orquestração ou em uma função de atividade por meio do método [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) em um parâmetro:

* **instanceId**: a ID exclusiva da instância.

O método retorna uma instância do [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) com as propriedades de cadeia de caracteres a seguir:

* **Id**: a ID da instância da orquestração (deve ser a mesma da entrada `InstanceId`).
* **StatusQueryGetUri**: a URL de status da instância de orquestração.
* **SendEventPostUri**: a URL "raise event" da instância de orquestração.
* **TerminatePostUri**: a URL "terminate" da instância de orquestração.
* **RewindPostUri**: a URL de "retroceder" do status da instância de orquestração.

As funções de atividade podem enviar uma instância de [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) para sistemas externos para monitorar ou elevar eventos para uma orquestração:

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

## <a name="rewinding-instances-preview"></a>Retroceder instâncias (versão prévia)

É possível *retroceder* uma instância de orquestração com falha para um estado anteriormente íntegro usando a API [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_). Isso funciona colocando a orquestração de volta no estado de *Execução* e executando novamente as falhas de execução de suborquestração e/ou atividade que causaram a falha de orquestração.

> [!NOTE]
> Essa API não pretende ser uma substituição para as políticas de repetição e de tratamento de erros apropriadas. Em vez disso, destina-se a ser usada apenas em casos em que as instâncias de orquestração falhem por motivos inesperados. Para obter mais detalhes sobre políticas de repetição e tratamento de erro, confira o tópico [Tratamento de erro](durable-functions-error-handling.md).

Um caso de uso de exemplo para *rewind* é um fluxo de trabalho que envolve uma série de [aprovações humanas](durable-functions-overview.md#pattern-5-human-interaction). Suponha que haja uma série de funções de atividade que notifique alguém sobre a necessidade de aprovação e aguarde a resposta em tempo real. Depois de todas as atividades de aprovação terem recebido respostas ou atingido o tempo limite, outra atividade falhará devido a um erro de configuração do aplicativo (por exemplo, uma cadeia de conexão de banco de dados inválida). O resultado é uma falha de orquestração profundamente no fluxo de trabalho. Com a API `RewindAsync`, um administrador do aplicativo pode corrigir o erro de configuração e *retroceder* a orquestração com falha de volta para o estado imediatamente anterior à falha. Nenhuma das etapas com interação humana precisa ser aprovada novamente e a orquestração pode agora ser concluída com êxito.

> [!NOTE]
> O recurso de *retroceder* não dá suporte para retroceder instâncias de orquestração que usam temporizadores duráveis.

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar APIs HTTP para o gerenciamento de instâncias](durable-functions-http-api.md)
