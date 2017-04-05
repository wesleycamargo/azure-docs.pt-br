---
title: "Logs de diagnóstico do Barramento de Serviço do Azure | Microsoft Docs"
description: "Saiba como configurar logs de diagnóstico para o Barramento de Serviço no Azure."
keywords: 
documentationcenter: .net
services: service-bus-messaging
author: banisadr
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2017
ms.author: babanisa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: b8ed786fe0c049d9be7ba1ca1cb6adef1950b8e9
ms.lasthandoff: 03/24/2017


---
# <a name="service-bus-diagnostic-logs"></a>Logs de diagnóstico do Barramento de Serviço

É possível exibir dois tipos de logs para o Barramento de Serviço do Azure:
* **[Logs de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Esses logs contém informações sobre as operações executadas em um trabalho. Os logs estão sempre habilitados.
* **[Logs de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. É possível configurar logs de diagnóstico para obter informações mais detalhadas sobre tudo o que acontece em um trabalho. Os logs de diagnóstico abrangem atividades desde o momento em que o trabalho é criado até sua exclusão, incluindo atualizações e atividades que ocorrem durante a execução do trabalho.

## <a name="turn-on-diagnostic-logs"></a>Ativar logs de diagnóstico
Os logs de diagnóstico estão desabilitados por padrão. Para habilitar logs de diagnóstico, faça o seguinte:

1.    No [Portal do Azure](https://portal.azure.com), acesse a folha do trabalho de streaming.

2.    Em **Monitoramento**, acesse a folha **Logs de diagnóstico**.

    ![navegação de folha para logs de diagnóstico](./media/service-bus-diagnostic-logs/image1.png)  

3.    Clique em **Ativar diagnóstico**.

    ![ativar logs de diagnóstico](./media/service-bus-diagnostic-logs/image2.png)

4.    Para **Status**, clique em **Ativar**.

    ![alterar logs de diagnóstico de status](./media/service-bus-diagnostic-logs/image3.png)

5.    Defina o destino de arquivamento desejado, por exemplo, uma conta de armazenamento, um Hub de Eventos ou o Log Analytics do Azure.

6.    Selecione as categorias de logs que você deseja coletar, por exemplo, **Execução** ou **Criação**.

7.    Salve as novas configurações de diagnóstico.

As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecerão no destino de arquivamento configurado, na folha **Logs de diagnóstico**.

Para saber mais sobre como configurar um diagnóstico, confira a [visão geral dos logs de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Esquema de logs de diagnóstico

Todos os logs são armazenados no formato JSON (JavaScript Object Notation). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito no exemplo a seguir.

## <a name="operation-logs-example"></a>Exemplo de logs de operação

Faz logon na categoria **OperationalLogs** para capturar o que acontece durante a operação do Barramento de Serviço. Especificamente, esses logs capturam o tipo de operação, incluindo a criação da fila, os recursos usados e o status da operação.

As cadeias de caracteres JSON do log de operação incluem os elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
ActivityId | ID interna, usada para acompanhamento
EventName | Nome da operação             
resourceId | ID de recurso do Azure Resource Manager
SubscriptionId | ID da assinatura
EventTimeString | Tempo de operação
EventProperties | Propriedades da operação
Status | Status da operação
Chamador | Chamador da operação (portal do Azure ou cliente de gerenciamento)
categoria | OperationalLogs

Este é um exemplo de uma cadeia de caracteres JSON do log de operação:

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
* [Introdução ao Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

