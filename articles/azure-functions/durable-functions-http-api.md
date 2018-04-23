---
title: APIs HTTP nas Funções Duráveis – Azure
description: Saiba como implementar APIs HTTP na extensão de Funções Duráveis do Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 07e6e5beb96042c2da82ac8be19e391d6153eabd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>APIs HTTP nas Funções Duráveis (Azure Functions)

A extensão de Tarefa Durável expõe um conjunto de APIs HTTP que podem ser usadas para executar as seguintes tarefas:

* Buscar o status de uma instância de orquestração.
* Enviar um evento a uma instância de orquestração em espera.
* Encerrar uma instância de orquestração em execução.


Cada uma dessas APIs HTTP é uma operação de webhook manipulada diretamente pela extensão de Tarefa Durável. Eles não são específicas a nenhuma função do aplicativo de funções.

> [!NOTE]
> Essas operações também podem ser invocadas diretamente usando as APIs de gerenciamento de instância na classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Para obter mais informações, consulte [Gerenciamento de Instâncias](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Descoberta de URL na API HTTP

A classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expõe uma API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) que pode ser usada para gerar uma carga de resposta HTTP que contém links para todas as operações com suporte. Veja um exemplo de função de gatilho HTTP que demonstra como usar essa API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Esta função de exemplo produz os seguintes dados de resposta JSON. O tipo de dados de todos os campos é `string`.

| Campo             |DESCRIÇÃO                           |
|-------------------|--------------------------------------|
| ID                |A ID da instância de orquestração. |
| statusQueryGetUri |A URL de status da instância de orquestração. |
| sendEventPostUri  |A URL "acionar evento" da instância de orquestração. |
| terminatePostUri  |A URL "encerrar" da instância de orquestração. |

Aqui está um exemplo de resposta:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> O formato das URLs de webhook pode variar dependendo de qual versão do host do Azure Functions você está executando. O exemplo acima refere-se ao host do Azure Functions 2.0.

## <a name="async-operation-tracking"></a>Acompanhamento de operação assíncrona

A resposta HTTP mencionada anteriormente foi criada para ajudar a implementar APIs assíncronas HTTP de execução longa com as Funções Duráveis. Às vezes, isso é chamado de *Padrão de consumidor de sondagem*. O fluxo de cliente/servidor funciona da seguinte maneira:

1. O cliente emite uma solicitação HTTP para iniciar um processo de execução longa, como uma função de orquestrador.
2. O gatilho HTTP de destino retorna uma resposta de HTTP 202 com um cabeçalho `Location` com o valor `statusQueryGetUri`.
3. O cliente sonda a URL no cabeçalho `Location`. Ela continua vendo respostas de HTTP 202 com um cabeçalho `Location`.
4. Quando a instância é concluída (ou falha), o ponto de extremidade no cabeçalho `Location` retorna HTTP 200.

Este protocolo permite coordenar processos de execução longa com clientes ou serviços externos que dão suporte a sondar um ponto de extremidade HTTP e a seguir o cabeçalho `Location`. As partes fundamentais já fazem parte das APIs HTTP das Funções Duráveis.

> [!NOTE]
> Todas as ações baseadas em HTTP fornecidas pelos [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/) dão suporte ao padrão de operação assíncrona padrão. Essa capacidade possibilita inserir uma função durável de execução longa como parte de um fluxo de trabalho dos Aplicativos Lógicos. Mais detalhes sobre o suporte dos Aplicativos Lógicos a padrões de HTTP assíncronos podem ser encontrados na [Documentação de ações e gatilhos de fluxo de trabalho dos Aplicativos Lógicos do Azure](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Referência de API HTTP

Todas as APIs HTTP implementadas pela extensão têm os seguintes parâmetros. O tipo de dados de todos os parâmetros é `string`.

| Parâmetro  | Tipo de Parâmetro  | DESCRIÇÃO |
|------------|-----------------|-------------|
| instanceId | URL             | A ID da instância de orquestração. |
| taskHub    | Cadeia de consulta    | O nome do [hub de tarefas](durable-functions-task-hubs.md). Se não for especificado, o nome do hub de tarefas do aplicativo de funções será presumido. |
| connection | Cadeia de consulta    | O **nome** da cadeia de conexão para a conta de armazenamento. Se não for especificada, a cadeia de conexão padrão do aplicativo de funções será presumida. |
| systemKey  | Cadeia de consulta    | A chave de autorização necessária para invocar a API. |
| showHistory| Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, o histórico de execução da orquestração será incluído na carga da resposta.| 
| showHistoryOutput| Cadeia de consulta    | Parâmetro opcional. Se definido como `true`, as saídas da atividade serão incluídas no histórico de execução da orquestração.| 

`systemKey` é uma chave de autorização gerada automaticamente pelo host do Azure Functions. Ela concede acesso especificamente às APIs da extensão de Tarefas Duráveis e pode ser gerenciada da mesma maneira que as [outras chaves de autorização](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). A maneira mais simples de descobrir o valor de `systemKey` é usar a API `CreateCheckStatusResponse` mencionada anteriormente.

As próximas seções tratam das APIs HTTP específicas com suporte da extensão e fornecem exemplos de como elas podem ser usadas.

### <a name="get-instance-status"></a>Obter status da instância

Obtém o status de uma instância de orquestração especificada.

#### <a name="request"></a>Solicitação

Para o Functions 1.0, o formato da solicitação é o seguinte:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

O formato do Functions 2.0 tem os mesmos parâmetros, mas tem um prefixo de URL ligeiramente diferente:

```http
GET /runtime/webhooks/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Response

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 200 (OK)**: a instância especificada está em um estado concluído.
* **HTTP 202 (Aceito)**: a instância especificada está em andamento.
* **HTTP 400 (Solicitação incorreta)**: a instância especificada falhou ou foi encerrada.
* **HTTP 404 (Não encontrado)**: a instância especificada não existe ou não começou a ser executada.

A carga de resposta para os casos de **HTTP 200** e **HTTP 202** é um objeto JSON com os campos a seguir:

| Campo           | Tipo de dados | DESCRIÇÃO |
|-----------------|-----------|-------------|
| runtimeStatus   | string    | O status de tempo de execução da instância. Os valores incluem *Em execução*, *Pendente*, *Falha*, *Cancelado*, *Encerrado*, *Concluído*. |
| input           | JSON      | Os dados JSON usados para inicializar a instância. |
| output          | JSON      | A saída JSON da instância. Este campo será `null` se a instância não estiver no estado concluído. |
| createdTime     | string    | A hora em que a instância foi criada. Usa a notação estendida ISO 8601. |
| lastUpdatedTime | string    | A hora em que a instância foi persistida pela última vez. Usa a notação estendida ISO 8601. |
| historyEvents   | JSON      | Uma matriz JSON contendo o histórico de execução da orquestração. Esse campo é `null`, exceto se o parâmetro da cadeia de caracteres de consulta `showHistory` estiver definido como `true`.  | 

Aqui está um exemplo de carga de resposta, incluindo o histórico de execução de orquestração e saídas de atividades (formatado para legibilidade):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

A resposta **HTTP 202** também inclui um cabeçalho de resposta **Local** que faz referência à mesma URL que o campo `statusQueryGetUri` mencionado anteriormente.

### <a name="raise-event"></a>Acionar evento

Envia uma mensagem de notificação de evento para uma instância de orquestração em execução.

#### <a name="request"></a>Solicitação

Para o Functions 1.0, o formato da solicitação é o seguinte:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

O formato do Functions 2.0 tem os mesmos parâmetros, mas tem um prefixo de URL ligeiramente diferente:

```http
POST /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o parâmetro exclusivo a seguir:

| Campo       | Tipo de parâmetro  | Tipo de dados | DESCRIÇÃO |
|-------------|-----------------|-----------|-------------|
| eventName   | URL             | string    | O nome do evento que a instância de orquestração de destino está esperando. |
| {content}   | Conteúdo da solicitação | JSON      | A carga do evento em formato JSON. |

#### <a name="response"></a>Response

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 202 (Aceito)**: o evento gerado foi aceito para processamento.
* **HTTP 400 (Solicitação incorreta)**: o conteúdo da solicitação não era do tipo `application/json` ou não era um JSON válido.
* **HTTP 404 (Não encontrado)**: a instância especificada não foi encontrada.
* **HTTP 410 (Não existe mais)**: a instância especificada foi concluída ou falhou e não pode processar os eventos gerados.

Veja um exemplo de solicitação que envia a cadeia de caracteres JSON `"incr"` para uma instância que aguarda um evento nomeado **operation**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

As respostas para esta API não têm nenhum conteúdo.

### <a name="terminate-instance"></a>Encerrar instância

Encerra uma instância de orquestração em execução.

#### <a name="request"></a>Solicitação

Para o Functions 1.0, o formato da solicitação é o seguinte:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

O formato do Functions 2.0 tem os mesmos parâmetros, mas tem um prefixo de URL ligeiramente diferente:

```http
DELETE /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Parâmetros de solicitação para essa API incluem o conjunto padrão mencionado anteriormente, bem como o seguinte parâmetro exclusivo.

| Campo       | Tipo de Parâmetro  | Tipo de Dados | DESCRIÇÃO |
|-------------|-----------------|-----------|-------------|
| reason      | Cadeia de consulta    | string    | Opcional. O motivo para encerrar a instância de orquestração. |

#### <a name="response"></a>Response

Vários valores de código de status possíveis podem ser retornados.

* **HTTP 202 (Aceito)**: a solicitação de encerramento foi aceita para processamento.
* **HTTP 404 (Não encontrado)**: a instância especificada não foi encontrada.
* **HTTP 410 (Não existe mais)**: a instância especificada foi concluída ou falhou.

Veja um exemplo de solicitação que encerra uma instância em execução e especifica o motivo **buggy**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

As respostas para esta API não têm nenhum conteúdo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como tratar erros](durable-functions-error-handling.md)
