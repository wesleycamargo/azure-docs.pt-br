---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a3af711503445000d9613feb2eec7967442fe538
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736069"
---
Definições de configuração para [Funções Duráveis](../articles/azure-functions/durable-functions-overview.md).

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

Nomes de hubs de tarefas devem começar com uma letra e devem ser compostos somente por letras e números. Se não for especificado, o nome do hub de tarefas padrão de um aplicativo de funções será **DurableFunctionsHub**. Para obter mais informações, consulte [Hubs de tarefas](../articles/azure-functions/durable-functions-task-hubs.md).

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Nomes alternativos para [hub de tarefas](../articles/azure-functions/durable-functions-task-hubs.md) podem ser usados para isolar vários aplicativos de Funções Duráveis uns dos outros, mesmo se eles estiverem usando o mesmo back-end de armazenamento.|
|ControlQueueBatchSize|32|O número de mensagens para efetuar pull da fila de controle por vez.|
|PartitionCount |4|A contagem de partição para a fila de controle. Pode ser um número inteiro positivo entre 1 e 16.|
|ControlQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de controle.|
|WorkItemQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de item de trabalho.|
|MaxConcurrentActivityFunctions |10 vezes o número de processadores no computador atual|O número máximo de funções de atividade que podem ser processadas simultaneamente em uma única instância de host.|
|MaxConcurrentOrchestratorFunctions |10 vezes o número de processadores no computador atual|O número máximo de funções do orquestrador que podem ser processadas simultaneamente em uma única instância do host.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|O nome da configuração de aplicativo que tem a cadeia de conexão do Armazenamento do Azure usada para gerenciar os recursos subjacentes do Armazenamento do Azure.|
|TraceInputsAndOutputs |falso|Um valor que indica se as entradas e saídas de chamadas de função sertão rastreadas. O comportamento padrão durante o rastreamento de eventos de execução de função é incluir o número de bytes nas entradas e saídas serializadas para chamadas de função. Isso fornece um mínimo de informações sobre como são as entradas e saídas sem sobrecarregar os logs ou expor inadvertidamente informações confidenciais para os logs. A definição dessa propriedade como true faz com que o log de função padrão registre todo o conteúdo de entradas e saídas da função.|
|LogReplayEvents|falso|Um valor que indica se é necessário gravar eventos de reprodução de orquestração para o Application Insights.|
|EventGridTopicEndpoint ||A URL de um ponto de extremidade de tópico personalizado da Grade de Eventos do Azure. Quando essa propriedade for definida, eventos de notificação de ciclo de vida de orquestração são publicados para esse ponto de extremidade. Esta propriedade dá suporte à resolução de Configurações do Aplicativo.|
|EventGridKeySettingName ||O nome da configuração de aplicativo que contém a chave usada para autenticar com o tópico personalizado da Grade de Eventos do Azure em `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|O número de novas tentativas se a publicação no Tópico de Grade de Eventos falha.|
|EventGridPublishRetryInterval|5 minutos|A Grade de Eventos publica o intervalo de repetição no formato *hh:mm:ss*.|

Muitos desses são para otimizar o desempenho. Para obter mais informações, consulte [Desempenho e escala](../articles/azure-functions/durable-functions-perf-and-scale.md).