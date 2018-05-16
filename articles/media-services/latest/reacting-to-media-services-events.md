---
title: Reagir aos eventos dos Serviços de Mídia do Azure | Microsoft Docs
description: Use a Grade de Eventos do Azure para assinar os eventos dos Serviços de Mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 969957d53824bd70440e5529b83bc830bb5d9cc4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="reacting-to-media-services-events"></a>Reagir aos eventos dos Serviços de Mídia

Os eventos dos Serviços de Mídia permitem aos aplicativos reagir a diferentes eventos (por exemplo, o evento de alteração de estado do trabalho) usando arquiteturas sem servidor modernas. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes. Em vez disso, os eventos são enviados por push pela [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) aos manipuladores de eventos como [Azure Functions](https://azure.microsoft.com/services/functions/), [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/), ou até mesmo seu próprio Webhook, e você só paga pelo que usa. Para saber mais sobre preços, confira [Preços da Grade de Eventos](https://azure.microsoft.com/pricing/details/event-grid/).

A disponibilidade para eventos dos Serviços de Mídia está vinculada à [disponibilidade](../../event-grid/overview.md) da Grade de Eventos e estará disponível em outras regiões, conforme a Grade de Eventos também for disponibilizada.  

## <a name="available-media-services-events"></a>Eventos de Serviços de Mídia disponíveis

A Grade de eventos usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes.  Atualmente, as assinaturas de evento dos Serviços de Mídia podem incluir o seguinte tipo de evento:  

|Nome do evento|DESCRIÇÃO|
|----------|-----------|
| Microsoft.Media.JobStateChange| Gerado quando o estado do trabalho muda. |

## <a name="event-schema"></a>Esquema do evento

Os eventos dos Serviços de Mídia contêm todas as informações que você precisa para responder às alterações em seus dados.  Você pode identificar um evento de Serviços de Mídia porque a propriedade eventType começa com "Microsoft.Media".

Para obter mais informações, confira [Esquemas de eventos dos Serviços de Mídia](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Práticas para consumo de eventos

Aplicativos que manipulam eventos de Serviços de Mídia devem seguir algumas práticas recomendadas:

* Como várias assinaturas podem ser configuradas para eventos de rota para o mesmo manipulador de eventos, é importante não supor que os eventos sejam de uma fonte específica, mas para verificar o tópico de mensagem a fim de garantir que ela venha da conta de armazenamento que você está esperando.
* Da mesma forma, verifique se o eventType é do tipo que você está preparado para processar, e não suponha que todos os eventos recebidos serão os tipos esperados.
* Ignore os campos que você não entende.  Essa prática ajudará você a manter-se resiliente a novos recursos que possam ser adicionados no futuro.
* Use a correspondência de prefixo e sufixo "subject" para limitar os eventos para um evento específico.

## <a name="next-steps"></a>Próximas etapas

[Obter eventos de estado do trabalho](job-state-events-cli-how-to.md)
