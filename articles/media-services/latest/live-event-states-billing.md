---
title: Estados do LiveEvent e faturamento no Azure Media Services|Microsoft Docs
description: Este tópico fornece uma visão geral dos estados e faturamento do LiveEvent dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 17fead25840e45f98478a6eb6c146bcc261dfe75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322311"
---
# <a name="live-event-states-and-billing"></a>Estados e cobrança de Evento ao Vivo

Nos Serviços de Mídia do Azure, um Evento ao Vivo iniciará a cobrança assim que o estado transitar para estado de **Execução**. Para interromper o Evento ao Vivo da cobrança é necessário parar o Evento ao Vivo.

Quando o **LiveEventEncodingType** no [Evento ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) estiver definido como Padrão, os Serviços de Mídia desligarão automaticamente todos os Eventos ao Vivo que ainda estejam no estado de **Execução** 12 horas após a perda do feed de entrada e não houver **Saídas Dinâmicas** em execução. No entanto, você ainda será cobrado pelo tempo que o Evento ao Vivo estava no estado de **Execução**.

## <a name="states"></a>Estados

O Evento ao Vivo pode estar em um dos seguintes estados.

|Estado|DESCRIÇÃO|
|---|---|
|**Interrompido**| Esse é o estado inicial do Evento ao Vivo após a criação (exceto se a inicialização automática estiver definida como verdadeira.) Não há cobrança nesse estado. Nesse estado, as propriedades do Evento ao Vivo poderão ser atualizadas, mas streaming não será permitido.|
|**Iniciando**| O Evento ao Vivo está sendo iniciado e os recursos estão sendo alocados. Não há cobrança nesse estado. Atualizações ou streaming não são permitidos durante esse estado. Se ocorrer um erro, o Evento ao Vivo retornará ao estado Parado.|
|**Executando**| Os recursos do Evento ao Vivo foram alocados, as URLs de visualização e ingestão foram geradas e está compatível para receber transmissões por streaming. Neste ponto, o faturamento está ativo. É necessário chamar explicitamente o recurso Parar no Evento ao Vivo para parar a cobrança adicional.|
|**Parando**| O Evento ao Vivo está sendo interrompido e os recursos estão sendo desprovisionados. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|
|**Excluindo**| O Evento ao Vivo está sendo excluído. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|

## <a name="next-steps"></a>Próximos passos

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)
