---
title: O que é a API de Detecção de Emoções?
titlesuffix: Azure Cognitive Services
description: Use o algoritmo de Detecção de Emoções baseado em nuvem para compilar aplicativos mais personalizados.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: overview
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 555b03be679b0d1ea61371d22ec9865e0e72b558
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215785"
---
# <a name="what-is-the-emotion-api"></a>O que é a API de Detecção de Emoções?

> [!IMPORTANT]
> A API de Detecção de Emoções será preterida em 15 de fevereiro de 2019. A funcionalidade de Detecção de Emoções agora está disponível como parte da [API de Detecção Facial](https://docs.microsoft.com/azure/cognitive-services/face/). 

Bem-vindo à API de Detecção de Emoções da Microsoft, que permite criar aplicativos mais personalizados com o algoritmo de reconhecimento de emoções baseada em nuvem.

### <a name="emotion-recognition"></a>Reconhecimento de emoções

A API de Detecção de Emoções pega uma imagem como uma entrada e retorna resultados confiáveis entre um conjunto de emoções para cada face na imagem, bem como a caixa delimitadora para a face com a API de Detecção Facial. As emoções detectadas são alegria, tristeza, surpresa, raiva, medo, desdém, aversão ou neutralidade. Esses emoções são comunicadas entre culturas e universalmente por meio das mesmas expressões faciais básicas, sendo estas identificadas pela API de Detecção de Emoções.

**Interpretando os resultados:**

Ao interpretar os resultados da API de Detecção de Emoções, a emoção detectada deve ser interpretada como a emoção com a pontuação mais alta, pois as pontuações são normalizadas para somarem um. Os usuários podem optar por definir um limite de confiança superior em seus aplicativos, dependendo de suas necessidades.

Para obter mais informações sobre a detecção de emoções, consulte a Referência de API:
  * Básico: se um usuário já tiver chamado a API de Detecção Facial, ele poderá enviar o retângulo de rosto como uma entrada e usar a camada Basic. [Referência de API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Padrão: se um usuário não enviar um retângulo de rosto, ele deverá usar o modo padrão.  [Referência de API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Para obter um exemplo sobre como interpretar o fluxo de vídeo com a API de Detecção de Emoções, consulte [Como analisar vídeos em tempo real](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
