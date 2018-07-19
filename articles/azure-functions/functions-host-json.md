---
title: Referência de host.json para Azure Functions
description: Documentação de referência do arquivo host.json do Azure Functions.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: tdykstra
ms.openlocfilehash: d89170f796355b734facc5e08ad1815a2b865d49
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342085"
---
# <a name="hostjson-reference-for-azure-functions"></a>Referência de host.json para Azure Functions

O arquivo de metadados *host.json* contém opções de configuração global que afetam todas as funções de um aplicativo de funções. Este artigo lista as configurações disponíveis. O esquema JSON está em http://json.schemastore.org/host.

Há outras opções de configuração global em [configurações de aplicativo](functions-app-settings.md) e no arquivo [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Arquivo host.json de exemplo

O arquivo *host.json* de exemplo a seguir tem todas as opções especificadas possíveis.

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

As seções seguintes deste artigo explicam cada propriedade de nível superior. Todas são opcionais, a menos que seja indicado o contrário.

## <a name="aggregator"></a>agregador

Especifica quantas invocações de função são agregadas ao [calcular métricas para o Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Propriedade |Padrão  | DESCRIÇÃO |
|---------|---------|---------| 
|batchSize|1000|Número máximo de solicitações para agregação.| 
|flushTimeout|00:00:30|Período de tempo máximo para agregação.| 

As invocações de função são agregadas quando o primeiro dos dois limites é atingido.

## <a name="applicationinsights"></a>applicationInsights

Controla o [recurso de amostragem no Application Insights](functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|isEnabled|verdadeiro|Habilita ou desabilita a amostragem.| 
|maxTelemetryItemsPerSecond|5|O limite em que a amostragem começa.| 

## <a name="durabletask"></a>durableTask

Definições de configuração para [Funções Duráveis](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

Nomes de hubs de tarefas devem começar com uma letra e devem ser compostos somente por letras e números. Se não for especificado, o nome do hub de tarefas padrão de um aplicativo de funções será **DurableFunctionsHub**. Para obter mais informações, consulte [Hubs de tarefas](durable-functions-task-hubs.md).

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Nomes alternativos para [hub de tarefas](durable-functions-task-hubs.md) podem ser usados para isolar vários aplicativos de Funções Duráveis uns dos outros, mesmo se eles estiverem usando o mesmo back-end de armazenamento.|
|ControlQueueBatchSize|32|O número de mensagens para efetuar pull da fila de controle por vez.|
|PartitionCount |4|A contagem de partição para a fila de controle. Pode ser um número inteiro positivo entre 1 e 16.|
|ControlQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de controle.|
|WorkItemQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de item de trabalho.|
|MaxConcurrentActivityFunctions |10 vezes o número de processadores no computador atual|O número máximo de funções de atividade que podem ser processadas simultaneamente em uma única instância de host.|
|MaxConcurrentOrchestratorFunctions |10 vezes o número de processadores no computador atual|O número máximo de funções de atividade que podem ser processadas simultaneamente em uma única instância de host.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|O nome da configuração de aplicativo que tem a cadeia de conexão do Armazenamento do Azure usada para gerenciar os recursos subjacentes do Armazenamento do Azure.|
|TraceInputsAndOutputs |falso|Um valor que indica se as entradas e saídas de chamadas de função sertão rastreadas. O comportamento padrão durante o rastreamento de eventos de execução de função é incluir o número de bytes nas entradas e saídas serializadas para chamadas de função. Isso fornece um mínimo de informações sobre como são as entradas e saídas sem sobrecarregar os logs ou expor inadvertidamente informações confidenciais para os logs. A definição dessa propriedade como true faz com que o log de função padrão registre todo o conteúdo de entradas e saídas da função.|
|LogReplayEvents|falso|Um valor que indica se é necessário gravar eventos de reprodução de orquestração para o Application Insights.|
|EventGridTopicEndpoint ||A URL de um ponto de extremidade de tópico personalizado da Grade de Eventos do Azure. Quando essa propriedade for definida, eventos de notificação de ciclo de vida de orquestração são publicados para esse ponto de extremidade. Esta propriedade dá suporte à resolução de Configurações do Aplicativo.|
|EventGridKeySettingName ||O nome da configuração de aplicativo que contém a chave usada para autenticar com o tópico personalizado da Grade de Eventos do Azure em `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|O número de novas tentativas se a publicação no Tópico de Grade de Eventos falha.|
|EventGridPublishRetryInterval|5 minutos|A nova tentativa do intervalo de publicação da Grade de Eventos no formato *hh:mm:ss*.|

Muitos desses são para otimizar o desempenho. Para obter mais informações, consulte [Desempenho e escala](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

Definições de configuração para [gatilhos e associações de Hub de Eventos](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>funções

Uma lista de funções que o host de trabalho executará. Uma matriz vazia significa que todas as funções serão executadas. Para uso somente quando [em execução localmente](functions-run-local.md). Em aplicativos de função, use a propriedade *function.json* `disabled` em vez da propriedade em *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica a duração do tempo limite para todas as funções. Nos planos de consumo, o intervalo válido é de 1 segundo a 10 minutos e o valor padrão é de 5 minutos. Nos planos de Serviço de Aplicativo, não há limite e o valor padrão é nulo, o que indica que não há nenhum tempo limite.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Definições de configuração para [monitor de integridade de Host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|Habilitado|verdadeiro|Se o recurso está habilitado. | 
|healthCheckInterval|10 segundos|O intervalo de tempo entre as verificações de integridade em segundo plano. | 
|healthCheckWindow|2 minutos|Uma janela de tempo deslizante usada em conjunto com a configuração `healthCheckThreshold`.| 
|healthCheckThreshold|6|Número máximo de vezes que a verificação de integridade pode falhar antes de uma reciclagem de host ser iniciada.| 
|counterThreshold|0.80|O limite no qual um contador de desempenho será considerado não íntegro.| 

## <a name="http"></a>http

Parâmetros de configuração para [gatilhos e associações http](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>ID

A ID exclusiva do host de trabalho. Pode ser uma GUID em letras minúsculas, sem traços. Obrigatório ao executar localmente. Quando em execução no Azure Functions, uma ID será gerada automaticamente se `id` for omitido.

Se você compartilhar uma conta de Armazenamento em vários aplicativos de funções, verifique se cada aplicativo de função tem um `id` diferente. É possível omitir a propriedade `id` ou definir manualmente cada aplicativo de funções `id` para um valor diferente. O gatilho de temporizador usa um bloqueio de armazenamento para garantir que haverá apenas uma instância de temporizador quando um aplicativo de funções escalar horizontalmente para várias instâncias. Se dois aplicativos de funções compartilharem o mesmo `id` e cada um usar um gatilho de temporizador, somente um temporizador irá executar.


```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>agente

Controles de filtragem de logs gravados por um [objeto ILogger](functions-monitoring.md#write-logs-in-c-functions) ou por [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|categoryFilter|n/d|Especifica a filtragem por categoria| 
|defaultLevel|Informações|Para as categorias não especificadas na matriz `categoryLevels`, envie logs nesse nível e acima para o Application Insights.| 
|categoryLevels|n/d|Uma matriz de categorias que especifica o nível mínimo de logs que será enviado ao Application Insights para cada categoria. A categoria especificada aqui controla todas as categorias que começam com o mesmo valor, com precedência para os valores maiores. No arquivo de exemplo *host.json* anterior, todas as categorias que começam com o log "Host.Aggregator" no nível `Information`. Todas as outras categorias que começam com o log "Host", como "Host.Executor", no nível `Error`.| 

## <a name="queues"></a>filas

Parâmetros de configuração para [gatilhos e associações de Armazenamento](functions-bindings-storage-queue.md).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

Parâmetro de configuração para [gatilhos e associações do Barramento de Serviço](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Parâmetro de configuração para o comportamento de bloqueio de Singleton. Para obter mais informações, consulte [Problema com o GitHub referente ao suporte de singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|lockPeriod|00:00:15|O período em que ocorrem os bloqueios de nível de função. Os bloqueios têm renovação automática.| 
|listenerLockPeriod|00:01:00|O período em que ocorrem os bloqueios de ouvinte.| 
|listenerLockRecoveryPollingInterval|00:01:00|O intervalo de tempo usado para a recuperação do bloqueio de ouvinte caso não tenha sido possível adquirir um bloqueio de ouvinte durante a inicialização.| 
|lockAcquisitionTimeout|00:01:00|A quantidade máxima de tempo em que o tempo de execução tenta adquirir um bloqueio.| 
|lockAcquisitionPollingInterval|n/d|O intervalo entre as tentativas de aquisição de bloqueio.| 

## <a name="tracing"></a>rastreamento

Parâmetros de configuração para logs que você cria usando um objeto `TraceWriter`. Consulte [Registro de logs em C#](functions-reference-csharp.md#logging) e [Registro de logs em Node.js](functions-reference-node.md#writing-trace-output-to-the-console). 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|consoleLevel|informações|O nível de rastreamento para o registro em log no console. As opções são: `off`, `error`, `warning`, `info` e `verbose`.|
|fileLoggingMode|debugOnly|O nível de rastreamento para registros em log de arquivo. As opções são: `never`, `always` e `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Um conjunto de [diretórios de código compartilhado](functions-reference-csharp.md#watched-directories) que devem ser monitorados quanto a alterações.  Garante que, quando o código nesses diretórios é alterado, as alterações sejam coletadas pelas funções.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como atualizar o arquivo host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte as configurações globais em variáveis de ambiente](functions-app-settings.md)
