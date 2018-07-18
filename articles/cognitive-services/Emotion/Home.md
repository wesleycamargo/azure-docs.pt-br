---
title: Visão geral da API de Detecção de Emoções | Microsoft Docs
description: Use o algoritmo de reconhecimento de emoção de última geração, baseado em nuvem da Microsoft para criar aplicativos mais personalizados, com a API de Detecção de Emoções nos Serviços Cognitivos.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 8383370cba3f78060e809f444f4ad3dab7380f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363745"
---
# <a name="what-is-emotion-api"></a>O que é a API de Detecção de Emoções?

> [!IMPORTANT]
> A API de Detecção de Emoções foi preterida no dia 30 de outubro de 2017. A funcionalidade é agora parte da [API de Detecção Facial](https://docs.microsoft.com/en-us/azure/cognitive-services/face/).

Bem-vindo à API de Detecção de Emoções da Microsoft, que permite criar aplicativos mais personalizados com o algoritmo de reconhecimento de emoções baseada em nuvem.

### <a name="emotion-recognition"></a>Reconhecimento de emoções

A API de Detecção de Emoções pega uma imagem como uma entrada e retorna resultados confiáveis entre um conjunto de emoções para cada face na imagem, bem como a caixa delimitadora para a face com a API de Detecção Facial. As emoções detectadas são alegria, tristeza, surpresa, raiva, medo, desdém, aversão ou neutralidade. Esses emoções são comunicadas entre culturas e universalmente por meio das mesmas expressões faciais básicas, sendo estas identificadas pela API de Detecção de Emoções. 

**Interpretando os resultados:** 

Ao interpretar os resultados da API de Detecção de Emoções, a emoção detectada deve ser interpretada como a emoção com a pontuação mais alta, pois as pontuações são normalizadas para somarem um. Os usuários podem optar por definir um limite de confiança superior em seus aplicativos, dependendo de suas necessidades. 

Para obter mais informações sobre a detecção de emoções, consulte a Referência de API: 
  * Basic: Se um usuário já chamou a API de Detecção Facial, ele pode enviar o retângulo da face como uma entrada e usar a camada Basic. [Referência de API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Se um usuário não enviar um retângulo da face, eles devem usar o modo Standard.  [Referência de API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Para obter um exemplo sobre como interpretar o fluxo de vídeo com a API de Detecção de Emoções, consulte [Como analisar vídeos em tempo real](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
