---
title: "Logs de diagnóstico do Barramento de Serviço do Azure | Microsoft Docs"
description: "Saiba mais sobre como analisar logs de diagnóstico do Barramento de Serviço no Microsoft Azure."
keywords: 
documentationcenter: 
services: service-bus-messaging
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/17/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 90321171586110a3b60c3df5b749003ebbf70ec9
ms.openlocfilehash: 70205b33e9d52e41f5c1a637fee4da192e2a971a
ms.lasthandoff: 02/22/2017


---
# <a name="service-bus-diagnostic-logs"></a>Logs de diagnóstico do Barramento de Serviço

## <a name="introduction"></a>Introdução
O Barramento de Serviço expõe dois tipos de logs: 
* [Logs de atividades](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) que estão sempre habilitados e fornecem informações sobre as operações realizadas em trabalhos;
* [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) que são configuráveis pelo usuário e fornecem informações mais avançadas sobre tudo o que acontece com o trabalho desde quando ele é criado, atualizado, enquanto ele está em execução e até ser excluído;

## <a name="how-to-enable-diagnostic-logs"></a>Como habilitar os logs de diagnóstico
Os logs de diagnóstico estão **desligados** por padrão. Para habilitá-los, siga estas etapas:

Entre no Portal do Azure e navegue até a folha de trabalho de streaming e use a folha de "Logs de diagnóstico" em "Monitoramento".

![navegação de folha para logs de diagnóstico](./media/service-bus-diagnostic-logs/image1.png)  

Em seguida, clique no link "Ativar diagnóstico"

![ativar logs de diagnóstico](./media/service-bus-diagnostic-logs/image2.png)

Nos diagnósticos abertos, altere o status para "Ligado".

![alterar logs de diagnóstico de status](./media/service-bus-diagnostic-logs/image3.png)

Configure o destino de arquivamento desejado (conta de armazenamento, hub de eventos, Log Analytics) e selecione as categorias de logs que você deseja coletar (Execução, Criação). Em seguida, salve a nova configuração de diagnóstico.

Uma vez salva, a configuração levará cerca de 10 minutos para entrar em vigor e após isso os logs começarão a aparecer no destino de arquivamento configurado, que pode ser visto na folha “Logs de diagnóstico”:

Há mais informações sobre a configuração de diagnóstico disponíveis na página [logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-schema"></a>Esquema de logs de diagnóstico

Todos os logs são armazenados no formato JSON e cada entrada tem campos de cadeia de caracteres que seguem o formato abaixo.

### <a name="operation-logs"></a>Logs de operação

O OperationalLogs captura o que está acontecendo durante a operação do Barramento de Serviço - especificamente o tipo de operação como criação de filas, recursos usados e o status da operação.

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
     "EventName": "Create Queue",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Barramento de Serviço](service-bus-messaging-overview.md)
* [Introdução ao Barramento de Serviço](service-bus-create-namespace-portal.md)

