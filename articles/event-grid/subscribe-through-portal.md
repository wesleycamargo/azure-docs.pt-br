---
title: Assinaturas da Grade de Eventos do Azure por meio do portal
description: Descreve como criar assinaturas da Grade de Eventos por meio do portal.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: tomfitz
ms.openlocfilehash: 72eaa17e78086a4e5338bb3198ef7471c44b785f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234613"
---
# <a name="subscribe-to-events-through-portal"></a>Assinar os eventos por meio do portal

Este artigo descreve como criar assinaturas da Grade de Eventos por meio do portal.

## <a name="create-event-subscriptions"></a>Criar assinaturas de evento

Para criar uma assinatura da Grade de Eventos para qualquer uma das [origens do evento](event-sources.md), use as seguinte etapas. Este artigo mostra como criar uma assinatura da Grade de Eventos para uma assinatura do Azure.

1. Selecione **Todos os serviços**.

   ![Selecionar todos os serviços](./media/subscribe-through-portal/select-all-services.png)

1. Pesquise **Assinaturas da Grade de Eventos** e selecione-as nas opções disponíveis.

   ![Search](./media/subscribe-through-portal/search.png)

1. Selecione **+ Assinatura de Evento**.

   ![Adicionar assinatura](./media/subscribe-through-portal/add-subscription.png)

1. Selecione o tipo de assinatura que você quer criar. Por exemplo, para assinar eventos da sua assinatura do Azure, selecione **Assinaturas do Azure** e a assinatura de destino.

   ![Selecionar assinatura do Azure](./media/subscribe-through-portal/azure-subscription.png)

1. Para assinar todos os tipos de eventos para essa origem do evento, mantenha a opção **Assinar todos os tipos de eventos** marcada. Caso contrário, selecione os tipos de eventos para essa assinatura.

   ![Selecionar tipos de eventos](./media/subscribe-through-portal/select-event-types.png)

1. Forneça detalhes adicionais sobre a assinatura do evento como o ponto de extremidade para manipular eventos e um nome da assinatura.

   ![Fornecer detalhes da assinatura](./media/subscribe-through-portal/provide-subscription-details.png)

## <a name="create-subscription-on-resource"></a>Criar assinatura no recurso

Algumas origens de eventos dão suporte para criação de uma assinatura de evento por meio da interface do portal para esse recurso. Selecione a origem do evento e procure **Eventos** no painel esquerdo.

![Fornecer detalhes da assinatura](./media/subscribe-through-portal/resource-events.png)

O portal apresenta opções para criar uma assinatura de evento relevante para essa origem.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre a entrega de eventos e repetições, [Entrega e repetição de mensagens da Grade de Eventos](delivery-and-retry.md).
* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para começar a usar rapidamente a Grade de Eventos, confira [Create and route custom events with Azure Event Grid](custom-event-quickstart.md) (Criar e rotear eventos personalizados com a Grade de Eventos do Azure).
