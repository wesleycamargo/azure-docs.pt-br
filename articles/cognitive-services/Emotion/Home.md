---
title: O que é a API de Detecção de Emoções?
titlesuffix: Azure Cognitive Services
description: Use o algoritmo de Detecção de Emoções baseado em nuvem para compilar aplicativos mais personalizados.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: overview
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: abf94e0ab6ebb3df649b1958503c086feb4fa19e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237119"
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
  * Basic: Se um usuário já chamou a API de Detecção Facial, ele pode enviar o retângulo da face como uma entrada e usar a camada Basic. [Referência de API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Se um usuário não enviar um retângulo da face, eles devem usar o modo Standard.  [Referência de API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Para obter um exemplo sobre como interpretar o fluxo de vídeo com a API de Detecção de Emoções, consulte [Como analisar vídeos em tempo real](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
