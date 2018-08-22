---
title: Conceitos do Azure Video Indexer | Microsoft Docs
description: Este tópico descreve alguns conceitos do serviço do Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 224c8b05027f51fb99c8d58be34c3604032c0f77
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399488"
---
# <a name="video-indexer-concepts"></a>Conceitos do Video Indexer
 
Este tópico descreve alguns conceitos do serviço do Video Indexer.
    
## <a name="summarized-insights"></a>Informações resumidas

Os insights resumidos contêm uma exibição agregada dos dados: faces (rostos), keywords (palavras-chave), sentiments (sentimentos). Por exemplo, em vez de percorrer cada um dos milhares de intervalos de tempo e verificar quais rostos estão nele, os insights resumidos contêm todos os rostos e, para cada um, os intervalos de tempo em que ele aparece e a porcentagem de tempo durante o qual ele é mostrado.

## <a name="topicskeywords"></a>Tópicos/palavras-chave

Os tópicos/palavras-chave estão na lista de frases-chave que o Video Indexer extrai do texto. Por exemplo, um vídeo de Scott Guthrie pode conter os seguintes tópicos/palavras-chave: Segurança, Azure, Microsoft Cloud, Receita.

## <a name="sentiments"></a>Sentimentos

Quando o Video Indexer analisa as transcrições, ele detecta sentimentos também. Por exemplo, "este é um evento muito animador" é um sentimento positivo.

## <a name="time-range-vs-adjusted-time-range"></a>intervalo de tempo versus intervalo de tempo ajustado

TimeRange é o intervalo de tempo no vídeo original. AdjustedTimeRange é o intervalo de tempo em relação à playlist atual. Como é possível criar uma playlist de diferentes linhas de diferentes vídeos, é possível usar um vídeo de 1 hora e usar apenas 1 linha dele, por exemplo, 10:00-10:15. Nesse caso, você terá uma playlist com 1 linha, em que o intervalo de tempo é 10:00-10:15, mas o adjustedTimeRange é 00:00-00:15.
 
## <a name="blocks"></a>Bloqueios

Os blocos destinam-se a tornar mais fácil percorrer os dados. Por exemplo, o bloco pode ser dividido com base em quando os locutores mudam ou quando há uma longa pausa.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como começar, consulte [How to sign up and upload your first video](video-indexer-get-started.md) (Como inscrever-se e carregar seu primeiro vídeo).

## <a name="see-also"></a>Consulte também

[Visão geral do indexador vídeo](video-indexer-overview.md)
