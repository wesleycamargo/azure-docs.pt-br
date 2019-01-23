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
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 19abd905bbc5e8ab724f7a2f25cb6da90db82841
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262635"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerenciar instâncias em Durable Functions no Azure

As instâncias de orquestração das [Funções Duráveis](durable-functions-overview.md) podem ser iniciadas, encerradas, consultadas e podem receber eventos de notificação. Todo o gerenciamento de instâncias é feito usando a [associação de cliente de orquestração](durable-functions-bindings.md). Este artigo mostra os detalhes de cada operação de gerenciamento de instância.

## <a name="starting-instances"></a>Iniciar instâncias

O método [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) em [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou `startNew` no `DurableOrchestrationClient` (JavaScript) inicia uma nova instância de uma função de orquestrador. Instâncias dessa classe podem ser obtidas usando a associação `orchestrationClient`. Internamente, esse método enfileira uma mensagem na fila de controle, que por sua vez dispara o início de uma função com o nome especificado que usa a associação de gatilho `orchestrationTrigger`.

Essa operação assíncrona é concluída quando o processo de orquestração é agendado com êxito. O processo de orquestração deve começar dentro de 30 segundos. Se demorar mais, um `TimeoutException` é gerado.

> [!WARNING]
> Ao desenvolver localmente em JavaScript, você precisará definir a variável de ambiente `WEBSITE_HOSTNAME` como `localhost:<port>`, por exemplo, `localhost:7071` para usar métodos em `DurableOrchestrationClient`. Para obter mais informações sobre esse requisito, confira o [Problema no GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

Os parâmetros para [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) são os seguintes:

* **Nome**: O nome da função de orquestrador a ser agendada.
* **Entrada**: Qualquer dado serializável em JSON, que deve ser passado como a entrada para a função de orquestrador.
* **InstanceId**: (opcional) a ID exclusiva da instância. Se não for especificada, uma ID de instância aleatória será gerada.

Veja um exemplo simples em C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

Os parâmetros para `startNew` são os seguintes:

* **Nome**: O nome da função de orquestrador a ser agendada.
* **InstanceId**: (opcional) a ID exclusiva da instância. Se não for especificada, uma ID de instância aleatória será gerada.
* **Entrada**: (opcional) Qualquer dado serializável em JSON, que deve ser passado como a entrada para a função de orquestrador.

Aqui está um exemplo de JavaScript simples:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!NOTE]
> Use um identificador aleatório para a ID de instância. Isso ajudará a garantir uma distribuição de carga igual ao dimensionar funções de orquestrador entre várias VMs. O momento adequado para usar IDs de instância não aleatórias é quando a ID precisar ser proveniente de uma fonte externa ou ao implementar o padrão de [orquestrador singleton](durable-functions-singletons.md).

### <a name="using-core-tools"></a>Usando Ferramentas de Núcleo

Também é possível iniciar uma instância diretamente por meio do comando [Azure Functions Core Tools](../functions-run-local.md) `durable start-new`. Ele usa os seguintes parâmetros:

* **`function-name` (obrigatório)**: Nome da função para iniciar
* **`input` (opcional)**: Entrada para a função, seja em linha ou por meio de um arquivo JSON de entrada. Para arquivos, prefixo do caminho para o arquivo com `@`, como `@path/to/file.json`.
* **`id` (opcional)**: ID da instância de orquestração. Se não fornecido, um GUID aleatório será gerado.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Ele também pode ser definido no [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

> [!NOTE]
> Os comandos Core Tools supõe que estão sendo executados a partir do diretório raiz de um aplicativo de funções. Se `connection-string-setting` e `task-hub-name` forem explicitamente fornecidos, os comandos podem ser executados a partir de qualquer diretório. Embora esses comandos possam ser executados sem um host de aplicativo de função em execução, alguns efeitos não podem ser observados, a menos que o host esteja em execução. Por exemplo, o `start-new` comando irá enfileirar uma mensagem de início para o hub de tarefas de destino, mas a orquestração não será executada, na verdade, a menos que haja um processo de host de aplicativo do função em execução e que pode processar a mensagem.

O comando a seguir seria iniciar a função chamada HelloWorld e passar o conteúdo do arquivo 'counter-data' a ela:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>Consultar instâncias

O método [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) na classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou o método `getStatus` na classe `DurableOrchestrationClient` (JavaScript) consulta o status de uma instância de orquestração.

Ele usa um `instanceId` (obrigatório), `showHistory` (opcional) e `showHistoryOutput` (opcional) e `showInput` (opcional, .NET apenas) como parâmetros.

* **`showHistory`**: Se definido para `true`, a resposta conterá o histórico de execução.
* **`showHistoryOutput`**: se definido como `true`, o histórico de execução conterá saídas da atividade.
* **`showInput`**: Se definido para `false`, a resposta conterá a entrada da função. O valor padrão é `true`. (.NET apenas)

O método retorna um objeto JSON com as seguintes propriedades:

* **Nome**: O nome da função de orquestrador.
* **InstanceId**: A ID da instância da orquestração (deve ser a mesma da entrada `instanceId`).
* **CreatedTime**: a hora em que a função de orquestrador começou a ser executada.
* **LastUpdatedTime**: A hora em que a orquestração passou por uma verificação pontual pela última vez.
* **Entrada**: A entrada da função como um valor JSON. Este campo não será preenchido se `showInput` for falso.
* **CustomStatus**: Status de orquestração personalizado no formato JSON.
* **Saída**: A saída da função como um valor JSON (se a função tiver sido concluída). Se a função de orquestrador tiver falhado, essa propriedade incluirá os detalhes da falha. Se a função de orquestrador tiver sido encerrada, essa propriedade incluirá o motivo fornecido para o encerramento (se houver).
* **RuntimeStatus**: Um dos seguintes valores:
  * **Pendente**: A instância foi agendada, mas ainda não foi iniciada em execução.
  * **Executando**: A instância começou a ser executada.
  * **Concluído**: A instância foi concluída normalmente.
  * **ContinuedAsNew**: A instância reiniciou a si mesma com um novo histórico. Esse estado é temporário.
  * **Falha**: A instância falhou com um erro.
  * **Encerrado**: A instância foi interrompida abruptamente.
* **Histórico**: Histórico de execução de orquestração. Este campo é preenchido somente se `showHistory` é definido como `true`.

Este método retornará `null` se a instância não existir ou não tiver começado a ser executada.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="using-core-tools"></a>Usando Ferramentas de Núcleo

Também é possível iniciar o status de uma instância de orquestração diretamente através do comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status`. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração
* **`show-input` (opcional)**: Se definido para `true`, a resposta conterá a entrada da função. O valor padrão é `false`.
* **`show-output` (opcional)**: Se definido para `true`, a resposta conterá a entrada da função. O valor padrão é `false`.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Ele também pode ser definido no [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

O comando a seguir recupera o status (incluindo entrada e saída) de uma instância com uma ID da instância de orquestração de 0ab8c55a66644d68a3a8b220b12d209c. Ele pressupõe que o `func` comando está sendo executado do diretório raiz do aplicativo de funções:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

O `durable get-history` comando pode ser usado para recuperar o histórico de uma instância de orquestração. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Também pode ser definido no host.json via durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>Consultar todas as instâncias

É possível usar o método `GetStatusAsync` (.NET) ou `getStatusAll` (JavaScript) para consultar os status de todas as instâncias de orquestração. Em .NET você pode passar um `CancellationToken` objeto caso queira cancelar. O método retorna objetos com as mesmas propriedades que o método `GetStatusAsync` com parâmetros.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-core-tools"></a>Usando Ferramentas de Núcleo

Também é possível consultar uma instância diretamente por meio do comando [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances`. Ele usa os seguintes parâmetros:

* **`top` (opcional)**: Este comando dá suporte à paginação. Esse parâmetro corresponde ao número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (opcional)**: Um token para indicar qual página/seção de instâncias a serem recuperados. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Ele também pode ser definido no [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>Consultar instâncias com filtros

Você também pode usar o método `GetStatusAsync` (.NET) ou `getStatusBy` (JavaScript) para obter uma lista de instâncias de orquestração que correspondem a um conjunto de filtros predefinidos. Opções de filtro possíveis incluem a hora de criação de orquestração e o status do tempo de execução da orquestração.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
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
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-the-functions-core-tools"></a>Usando o Functions Core Tools

O `durable get-instances` comando também pode ser usado com filtros. Além dos parâmetros mencionados anteriormente `top`, `continuation-token`, `connection-string-setting`, e `task-hub-name`, três parâmetros de filtro (`created-after`, `created-before`, e `runtime-status`), podem ser usados.

* **`created-after` (opcional)**: Recuperar as instâncias criadas após essa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`created-before` (opcional)**: Recuperar as instâncias criadas antes dessa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`runtime-status` (opcional)**: Recuperar as instâncias cujo status combina esses ('running', 'completed', etc.). Pode fornecer o status de vários (separado por espaço).
* **`top` (opcional)**: Número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (opcional)**: Um token para indicar qual página/seção de instâncias a serem recuperados. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Ele também pode ser definido no [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

Se nenhum filtro (`created-after`, `created-before`, ou `runtime-status`) for fornecido, em seguida, `top` instâncias serão recuperadas com nenhuma relação à hora de criação ou status de tempo de execução.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>Encerrar instâncias

Uma instância de orquestração em execução pode ser encerrada usando o método [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou o método `terminate` da classe `DurableOrchestrationClient` (JavaScript). Os dois parâmetros são um `instanceId` e uma cadeia de caracteres `reason`, que serão gravados em logs no status da instância. Uma instância encerrada deixará de ser executada assim que atingir o próximo ponto `await` (.NET) ou `yield` (JavaScript) ou será encerrada imediatamente se já estiver em um `await` (.NET) ou `yield` (JavaScript).

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> O encerramento de instância não propaga no momento. As funções de atividade e as suborquestrações serão executadas por completo, independentemente de a instância de orquestração que as chamou ter sido encerrada.

### <a name="using-core-tools"></a>Usando Ferramentas de Núcleo

Também é possível encerrar o status de uma instância de orquestração diretamente através do comando [Core Tools](../functions-run-local.md) `durable terminate`. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração para encerrar
* **`reason` (opcional)**: Motivo do término
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Ele também pode ser definido no [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

O comando a seguir encerra uma instância de orquestração com uma ID de 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>Enviar eventos para instâncias

Notificações de eventos podem ser enviadas a instâncias em execução usando o método [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou o método `raiseEvent` da classe `DurableOrchestrationClient` (JavaScript). Instâncias que podem lidar com esses eventos são aquelas que estão aguardando uma chamada para [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) ou `waitForExternalEvent` (JavaScript).

Os parâmetros para [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) e `raiseEvent` (JavaScript) são conforme a seguir:

* **InstanceId**: A ID exclusiva da instância.
* **EventName**: Nome do evento a ser enviado.
* **EventData**: Uma carga serializável em JSON para enviar para a instância.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!WARNING]
> Se não houver nenhuma instância de orquestração com a *ID de instância* especificada ou se a instância não estiver aguardando o *nome do evento* especificado, a mensagem de evento será descartada. Para obter mais informações sobre esse comportamento, consulte o [Problema no GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="using-core-tools"></a>Usando Ferramentas de Núcleo

Também é possível acionar um evento para uma instância de orquestração diretamente através do comando [Core Tools](../functions-run-local.md) `durable raise-event`. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração
* **`event-name` (opcional)**: Nome do evento para gerar. O padrão é `$"Event_{RandomGUID}"`
* **`event-data` (opcional)**: Dados a serem enviados para a instância de orquestração. Isso pode ser o caminho para um arquivo JSON ou os dados podem ser fornecidos diretamente na linha de comando
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Ele também pode ser definido no [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguardar a conclusão da orquestração

A classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expõe uma API [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) em .NET que pode ser usada para obter sincronicamente a saída real de uma instância de orquestração. No JavaScript, a `DurableOrchestrationClient` classe expõe uma `waitForCompletionOrCreateCheckStatusResponse` API para a mesma finalidade. O método usa o valor padrão de 10 segundos para `timeout` e 1 segundo para `retryInterval` quando eles não estão definidos.  

Veja um exemplo de função de gatilho HTTP que demonstra como usar essa API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

A função pode ser chamada com a seguinte linha usando o tempo limite de 2 segundos e o intervalo de repetição de 0,5 segundo:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, há dois casos:

* As instâncias de orquestração são concluídas dentro do tempo limite definido (neste caso, 2 segundos), a resposta é a saída de instância real de orquestração entregue sincronicamente:

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

* As instâncias de orquestração não podem ser concluídas dentro do tempo limite definido (neste caso, 2 segundos), a resposta é o padrão descrito em **descoberta de URL HTTP da API**:

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
> O formato das URLs de webhook pode variar dependendo de qual versão do host do Azure Functions você está executando. O exemplo acima refere-se ao host do Azure Functions 2.x.

## <a name="retrieving-http-management-webhook-urls"></a>Recuperar URLs do WebHok de gerenciamento de HTTP

Os sistemas externos podem se comunicar com as Funções Duráveis por meio das URLs do webhook que fazem parte da resposta padrão descrita na [descoberta de URL na API HTTP](durable-functions-http-api.md). No entanto, as URLs do webhook também podem ser acessadas programaticamente no cliente de orquestração ou em uma função de atividade por meio do método [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) da classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) ou o método `createHttpManagementPayload` da classe `DurableOrchestrationClient` (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) e `createHttpManagementPayload` têm um parâmetro:

* **instanceId**: A ID exclusiva da instância.

Os métodos retornam uma instância do [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) ou um objeto (JavaScript) com as seguintes propriedades de cadeia de caracteres:

* **Id**: A ID da instância da orquestração (deve ser a mesma da entrada `InstanceId`).
* **StatusQueryGetUri**: A URL de status da instância de orquestração.
* **SendEventPostUri**: A URL "acionar evento" da instância de orquestração.
* **TerminatePostUri**: A URL "encerrar" da instância de orquestração.
* **RewindPostUri**: A URL para “retroceder” do status da instância de orquestração.

As funções de atividade podem enviar uma instância desses objetos para sistemas externos para monitorar ou elevar eventos para uma orquestração:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewinding-instances-preview"></a>Retroceder instâncias (versão prévia)

É possível *retroceder* uma instância de orquestração com falha para um estado anteriormente íntegro usando a API[RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) ou `rewindAsync` (JavaScript). Isso funciona colocando a orquestração de volta no estado de *Execução* e executando novamente as falhas de execução de suborquestração e/ou atividade que causaram a falha de orquestração.

> [!NOTE]
> Essa API não pretende ser uma substituição para as políticas de repetição e de tratamento de erros apropriadas. Em vez disso, destina-se a ser usada apenas em casos em que as instâncias de orquestração falhem por motivos inesperados. Para obter mais detalhes sobre políticas de repetição e tratamento de erro, confira o tópico [Tratamento de erro](durable-functions-error-handling.md).

Um caso de uso de exemplo para *rewind* é um fluxo de trabalho que envolve uma série de [aprovações humanas](durable-functions-concepts.md#human). Suponha que haja uma série de funções de atividade que notifique alguém sobre a necessidade de aprovação e aguarde a resposta em tempo real. Depois de todas as atividades de aprovação terem recebido respostas ou atingido o tempo limite, outra atividade falhará devido a um erro de configuração do aplicativo (por exemplo, uma cadeia de conexão de banco de dados inválida). O resultado é uma falha de orquestração profundamente no fluxo de trabalho. Com a API `RewindAsync` (.NET) ou `rewindAsync` (JavaScript), um administrador do aplicativo pode corrigir o erro de configuração e *retrocede* a orquestração com falha de volta para o estado imediatamente anterior à falha. Nenhuma das etapas com interação humana precisa ser aprovada novamente e a orquestração pode agora ser concluída com êxito.

> [!NOTE]
> O recurso de *retroceder* não dá suporte para retroceder instâncias de orquestração que usam temporizadores duráveis.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (apenas Funções 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="using-core-tools"></a>Usando Ferramentas de Núcleo

Também é possível retroceder o status de uma instância de orquestração diretamente através do comando [Core Tools](../functions-run-local.md) `durable rewind`. Ele usa os seguintes parâmetros:

* **`id` (obrigatório)**: ID da instância de orquestração
* **`reason` (opcional)**: O motivo para retroceder a instância de orquestração
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Ele também pode ser definido no [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Limpar o histórico de instância

> [!NOTE]
> A `PurgeInstanceHistoryAsync` API está disponível atualmente apenas para C#. Ele será adicionado ao JavaScript em uma versão futura.

O histórico de orquestração pode ser limpo usando [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_). A funcionalidade removerá todos os dados associados a uma orquestração - linhas de tabela do Azure e blobs de mensagens grandes se eles existirem. Esse método tem duas sobrecargas. A primeira limpa o histórico por ID da instância de orquestração:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

O segundo exemplo mostra uma função disparada por temporizador que limpa o histórico de todas as instâncias de orquestração concluída após o intervalo de tempo especificado. Nesse caso, ele removerá os dados para todas as instâncias concluídas há 30 ou mais dias atrás. Ele é agendado para ser executado uma vez por dia às 00h:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> A sobrecarga *PurgeInstanceHistory* aceitando o período de tempo como parâmetro processará somente as instâncias de orquestração em um dos status tempo de execução - Concluído, Encerrado ou Com falha.

### <a name="using-core-tools"></a>Usando Ferramentas de Núcleo

É possível limpar o histórico de uma instância de orquestração usando o comando [Core Tools](../functions-run-local.md)`durable purge-history`. Semelhante ao segundo C# exemplo acima, ele limpa o histórico de todas as instâncias de orquestração criadas durante um intervalo de tempo especificado. As instâncias limpas podem ser filtradas por status de tempo de execução. O comando tem vários parâmetros:

* **`created-after` (opcional)**: Limpar as instâncias criadas após essa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`created-before` (opcional)**: Limpar as instâncias criadas antes dessa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`runtime-status` (opcional)**: Limpar o histórico das instâncias cujo estado corresponda a esses (“em execução”, “concluído” etc.). Pode fornecer o status de vários (separado por espaço).
* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Ele também pode ser definido no [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

O comando a seguir exclui o histórico de todas as instâncias com falha criados antes de 14 de novembro de 2018 às 19h35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>Excluir um Hub de tarefas

Usando o comando [Core Tools](../functions-run-local.md) `durable delete-task-hub`, é possível excluir todos os artefatos de armazenamento associados a um hub de tarefas específico. Isso inclui tabelas de armazenamento, filas e blobs do Azure. O comando tem dois parâmetros:

* **`connection-string-setting` (opcional)**: Nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (opcional)**: Nome do hub de tarefas durável a ser usado. O padrão é DurableFunctionsHub. Ele também pode ser definido no [host.json](durable-functions-bindings.md#host-json) via durableTask:HubName.

O comando a seguir exclui todos os dados de armazenamento do Azure associados com o hub de tarefas 'UserTest'.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como usar APIs HTTP para o gerenciamento de instâncias](durable-functions-http-api.md)
