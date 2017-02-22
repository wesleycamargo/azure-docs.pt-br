---
title: "Logs de diagnóstico dos Hubs de Eventos do Azure | Microsoft Docs"
description: "Saiba mais sobre como analisar logs de diagnóstico dos Hubs de Eventos no Microsoft Azure."
keywords: 
documentationcenter: 
services: event-hubs
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 4d7d0e1f5ffb395bf91bbdac1ab4b9ec3f9dee1c
ms.openlocfilehash: cb8c7930ee423543c91366de64220ee55609a4cf


---
# <a name="event-hub-diagnostic-logs"></a>Logs de diagnóstico de Hub de Eventos

## <a name="introduction"></a>Introdução
Os Hubs de Eventos expõem dois tipos de logs: 
* [Logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) que estão sempre habilitados e fornecem informações sobre as operações realizadas em trabalhos;
* [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) que são configuráveis pelo usuário e fornecem informações mais avançadas sobre tudo o que acontece com o trabalho desde quando ele é criado, atualizado, enquanto ele está em execução e até ser excluído;

## <a name="how-to-enable-diagnostic-logs"></a>Como habilitar os logs de diagnóstico
Os logs de diagnóstico estão **desligados** por padrão. Para habilitá-los, siga estas etapas:

Entre no Portal do Azure e navegue até a folha de trabalho de streaming e use a folha de "Logs de diagnóstico" em "Monitoramento".

![navegação de folha para logs de diagnóstico](./media/event-hubs-diagnostic-logs/image1.png)  

Em seguida, clique no link "Ativar diagnóstico"

![ativar logs de diagnóstico](./media/event-hubs-diagnostic-logs/image2.png)

Nos diagnósticos abertos, altere o status para "Ligado".

![alterar logs de diagnóstico de status](./media/event-hubs-diagnostic-logs/image3.png)

Configure o destino de arquivamento desejado (conta de armazenamento, hub de eventos, Log Analytics) e selecione as categorias de logs que você deseja coletar (Execução, Criação). Em seguida, salve a nova configuração de diagnóstico.

Uma vez salva, a configuração levará cerca de 10 minutos para entrar em vigor e após isso os logs começarão a aparecer no destino de arquivamento configurado, que pode ser visto na folha “Logs de diagnóstico”:

Há mais informações sobre a configuração de diagnóstico disponíveis na página [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-categories"></a>Categorias dos logs de diagnóstico
Há duas categorias de logs de diagnóstico que capturamos no momento:

* **ArchivalLogs:** capture os logs relacionados ao arquivamento de Hub de Eventos - especificamente relacionados a erros de arquivo morto.
* **OperationalLogs:** captura o que está acontecendo durante a operação de Hubs de Eventos - especificamente o tipo de operação como criação de Hub de Eventos, recursos usados e o status da operação.

## <a name="diagnostic-logs-schema"></a>Esquema de logs de diagnóstico
Todos os logs são armazenados no formato JSON e cada entrada tem campos de cadeia de caracteres que seguem o formato abaixo:


### <a name="archive-error-schema"></a>Esquema de erro de arquivo morto
Nome | Descrição
------- | -------
TaskName | A descrição da tarefa que falhou
ActivityId | Id interna para finalidade de controle
trackingId | Id interna para finalidade de controle
resourceId | ID de Recurso ARM
eventHub | Nome completo do Hub de Eventos (inclui o nome do namespace)
partitionId | A partição que está sendo gravada no Hub de Eventos
archiveStep | ArchiveFlushWriter
startTime | Hora de início de falha
failures | Número de vezes que ocorreu falha
durationInSeconds | Duração de falha
Message | Mensagem de erro
categoria | ArchiveLogs

#### <a name="example-archive-log"></a>Log de arquivo de exemplo

```json
{
     "TaskName": "EventHubArchiveUserError",
     "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
     "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
     "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
     "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
     "partitionId": "1",
     "archiveStep": "ArchiveFlushWriter",
     "startTime": "9/22/2016 5:11:21 AM",
     "failures": 3,
     "durationInSeconds": 360,
     "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
     "category": "ArchiveLogs"
}
```

### <a name="operation-logs-schema"></a>Esquema de logs de operação
Nome | Descrição
------- | -------
ActivityId | Id interna para finalidade de controle
EventName | Nome da operação           
resourceId | ID de Recurso ARM
SubscriptionId | ID da assinatura
EventTimeString | Tempo de operação
EventProperties | Propriedades da operação
Status | Status da operação
Chamador | Chamador de operação (Portal ou cliente de gerenciamento)
categoria | OperationalLogs

#### <a name="example-operation-log"></a>Log de operação de exemplo

```json
Example: 
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create EventHub",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Próximas etapas
* [Introdução aos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Visão geral da API dos Hubs de Eventos](event-hubs-api-overview.md)
* [Introdução aos Hubs de Eventos](event-hubs-csharp-ephcs-getstarted.md)


<!--HONumber=Feb17_HO1-->


