---
title: Reagir aos eventos dos Serviços de Mídia do Azure | Microsoft Docs
description: Use a Grade de Eventos do Azure para assinar os eventos dos Serviços de Mídia.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 3541a5b33aa0bb98d9381b51caefc63b6aa677ad
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377541"
---
# <a name="handling-event-grid-events"></a>Lidando com os eventos da Grade de Eventos

Os eventos dos Serviços de Mídia permitem aos aplicativos reagir a diferentes eventos (por exemplo, o evento de alteração de estado do trabalho) usando arquiteturas sem servidor modernas. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes. Em vez disso, os eventos são enviados por push pela [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) aos manipuladores de eventos como [Azure Functions](https://azure.microsoft.com/services/functions/), [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/), ou até mesmo seu próprio Webhook, e você só paga pelo que usa. Para saber mais sobre preços, confira [Preços da Grade de Eventos](https://azure.microsoft.com/pricing/details/event-grid/).

A disponibilidade para eventos dos Serviços de Mídia está vinculada à [disponibilidade](../../event-grid/overview.md) da Grade de Eventos e estará disponível em outras regiões, conforme a Grade de Eventos também for disponibilizada.  

## <a name="available-media-services-events"></a>Eventos de Serviços de Mídia disponíveis

A Grade de eventos usa [assinaturas de evento](../../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes.  Atualmente, as assinaturas de evento dos Serviços de Mídia podem incluir os seguintes eventos:  

|Nome do evento|DESCRIÇÃO|
|----------|-----------|
| Microsoft.Media.JobStateChange| Gerado quando o estado do trabalho muda. |
| Microsoft.Media.LiveEventConnectionRejected | A tentativa de conexão do codificador foi rejeitada. |
| Microsoft.Media.LiveEventEncoderConnected | O codificador estabelece conexão com o evento ao vivo. |
| Microsoft.Media.LiveEventEncoderDisconnected | O codificador desconecta. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | O servidor de mídia descarta parte dos dados porque o tempo limite deles foi atingido ou eles têm um carimbo de data/hora de sobreposição (o carimbo de data/hora da nova parte de dados é anterior à hora de término da parte de dados anterior). |
| Microsoft.Media.LiveEventIncomingStreamReceived | O servidor de mídia recebe a primeira parte de dados para cada faixa no fluxo ou na conexão. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | O servidor de mídia detecta que os fluxos de áudio e vídeo estão fora de sincronização. Use como um aviso porque a experiência do usuário não pode ser afetada. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | O servidor de mídia detecta que um dos dois fluxos de vídeo provenientes do codificador externo está fora de sincronia. Use como um aviso porque a experiência do usuário não pode ser afetada. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publicado a cada 20 segundos para cada faixa quando o evento ao vivo está em execução. Fornece resumo de integridade de ingestão. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | O servidor de mídia detecta descontinuidade na faixa de entrada. |

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
