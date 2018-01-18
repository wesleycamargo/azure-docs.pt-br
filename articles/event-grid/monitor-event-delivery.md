---
title: Monitorar a entrega de mensagens da Grade de Eventos do Azure
description: Descreve como monitorar a entrega de mensagens da Grade de Eventos do Azure.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/10/2018
ms.author: tomfitz
ms.openlocfilehash: 1552174589e91c370c3b85a9af7b5cc1106cbb90
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
# <a name="monitor-event-grid-message-delivery"></a>Monitorar a entrega de mensagens da Grade de Eventos 

Este artigo descreve como usar o portal para ver o status de entregas de evento.

A entrega proporcionada pela Grade de Eventos tem um tempo de duração. Cada mensagem é entregue pelo menos uma vez para cada assinatura. Os eventos são imediatamente enviados ao webhook registrado de cada assinatura. Se um webhook não confirmar o recebimento de um evento em até 60 segundos a contar da primeira tentativa de entrega, a Grade de Eventos repete a entrega do evento.

Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Métricas de entrega

O portal exibe a métrica do status de entrega das mensagens de evento.

Para tópicos, as métricas são:

* **Publicação bem-sucedida**: evento enviado com êxito para o tópico e processado com uma resposta 2xx.
* **Falha na Publicação**: evento enviado para o tópico, mas rejeitado com um código de erro.
* **Sem correspondência**: evento publicado com êxito para o tópico, mas não corresponde a uma assinatura de evento. O evento foi cancelado.

Para assinaturas, as métricas são:

* **Entrega bem-sucedida**: evento entregue com êxito ao ponto de extremidade da assinatura e recebeu uma resposta de 2xx.
* **Falha de entrega**: evento enviado ao ponto de extremidade da assinatura, mas recebeu uma resposta 4xx ou 5xx.
* **Eventos Expirados**: evento não foi entregue e todas as tentativas de repetição foram enviadas. O evento foi cancelado.
* **Eventos correspondidos**: evento no tópico foi correspondido pela assinatura do evento.

## <a name="event-subscription-status"></a>Status da assinatura do evento

Para ver as métricas de uma assinatura de evento, procure **Assinaturas da Grade de Eventos** nos serviços disponíveis e selecione-a.

![Pesquisar assinaturas de evento](./media/monitor-event-delivery/select-event-subscriptions.png)

Filtre por tipo de evento, assinatura e localização. Selecione **Métricas** da assinatura para exibir.

![Filtrar assinaturas de evento](./media/monitor-event-delivery/filter-events.png)

Exiba as métricas do tópico e assinatura do evento.

![Exibir métricas de evento](./media/monitor-event-delivery/subscription-metrics.png)

## <a name="custom-event-status"></a>Status de evento personalizado

Se você publicou um tópico personalizado, pode exibir as métricas dele. Selecione o grupo de recursos que contém o tópico e selecione o tópico.

![Selecionar tópico personalizado](./media/monitor-event-delivery/select-custom-topic.png)

Exiba as métricas do tópico de evento personalizado.

![Exibir métricas de evento](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
