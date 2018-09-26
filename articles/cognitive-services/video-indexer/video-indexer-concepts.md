---
title: Conceitos do Video Indexer
titlesuffix: Azure Cognitive Services
description: Este tópico descreve alguns conceitos do serviço do Video Indexer.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 740f13e90397650ed9274937b16254e46c6deced
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984113"
---
# <a name="video-indexer-concepts"></a>Conceitos do Video Indexer
 
Este artigo descreve alguns conceitos do serviço do Video Indexer.
    
## <a name="summarized-insights"></a>Informações resumidas

Os insights resumidos contêm uma exibição agregada dos dados: rostos, tópicos, emoções. Por exemplo, em vez de percorrer cada um dos milhares de intervalos de tempo e verificar quais rostos estão nele, os insights resumidos contêm todos os rostos e, para cada um, os intervalos de tempo em que ele aparece e a porcentagem de tempo durante o qual ele é mostrado.

## <a name="time-range-vs-adjusted-time-range"></a>intervalo de tempo versus intervalo de tempo ajustado

TimeRange é o intervalo de tempo no vídeo original. AdjustedTimeRange é o intervalo de tempo em relação à playlist atual. Como é possível criar uma playlist de diferentes linhas de diferentes vídeos, é possível usar um vídeo de 1 hora e usar apenas 1 linha dele, por exemplo, 10:00-10:15. Nesse caso, você terá uma playlist com 1 linha, em que o intervalo de tempo é 10:00-10:15, mas o adjustedTimeRange é 00:00-00:15.
 
## <a name="blocks"></a>Bloqueios

Os blocos destinam-se a tornar mais fácil percorrer os dados. Por exemplo, o bloco pode ser dividido com base em quando os locutores mudam ou quando há uma longa pausa.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como começar, consulte [How to sign up and upload your first video](video-indexer-get-started.md) (Como inscrever-se e carregar seu primeiro vídeo).

## <a name="see-also"></a>Consulte também

[Visão geral do indexador vídeo](video-indexer-overview.md)
