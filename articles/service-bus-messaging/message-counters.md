---
title: Contagem de mensagens do Barramento de Serviço do Azure | Microsoft Docs
description: Recupere a contagem de mensagens do Barramento de Serviço do Azure.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: adfd8c5849cfee69805715378a3f56ec9f685b00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403951"
---
# <a name="message-counters"></a>Contadores de mensagens

Você pode recuperar a contagem de mensagens mantidas em filas e assinaturas usando o Azure Resource Manager e as APIs [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) do Barramento de Serviço no SDK do .NET Framework.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o PowerShell, você pode obter a contagem da seguinte maneira:

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Detalhes da contagem de mensagens

Saber a contagem de mensagens ativas é útil para determinar se uma fila cria uma lista de pendências que requer mais recursos para processar do que o que foi implantado atualmente. Os detalhes de contador a seguir estão disponíveis na classe [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails):

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): As mensagens na fila ou na assinatura que estão no estado ativo e estão prontas para entrega.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): Mensagens na fila de mensagens mortas.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): Mensagens no estado agendado.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): As mensagens com falha na transferência para outra fila ou tópico e que foram movidas para a fila de mensagens mortas de transferência.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): As mensagens pendentes são transferidas para outra fila ou tópico.

Se um aplicativo desejar dimensionar os recursos com base no comprimento da fila, ele deverá fazer isso com um ritmo calculado. A aquisição dos contadores de mensagem é uma operação cara dentro do agente de mensagens e executá-la com frequência afeta de forma direta e adversa o desempenho da entidade.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
