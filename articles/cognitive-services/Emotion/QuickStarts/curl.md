---
title: 'Início rápido: Reconhecer emoções em rostos em uma imagem – API de Detecção de Emoções, cURL'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de códigos para ajudar você a começar a usar a API de Detecção de Emoções com cURL.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bd157bfeff8ceaa18a75f15895b7304385c5e522
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232030"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Início Rápido: Criar um aplicativo para reconhecer emoções em rostos em uma imagem.

> [!IMPORTANT]
> A API de Detecção de Emoções será preterida em 15 de fevereiro de 2019. A funcionalidade de Detecção de Emoções agora está disponível como parte da [API de Detecção Facial](https://docs.microsoft.com/azure/cognitive-services/face/).

Este artigo fornece informações e exemplos de código para ajudar você a começar a usar o [método de reconhecimento da API de Detecção de Emoções](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) com cURL para reconhecer as emoções expressadas por uma ou mais pessoas em uma imagem.

## <a name="prerequisite"></a>Pré-requisito
* Obtenha sua chave de assinatura gratuita [aqui](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-curl-example-request"></a>Solicitação de exemplo cURL de Detecção de Emoções

> [!NOTE]
> Você deve usar o mesmo local em sua chamada REST que é usado para obter as chaves de assinatura. Por exemplo, se você adquiriu suas chaves de assinatura de westcentralus, substitua "westus" na URL abaixo "westcentralus".

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}"
```

## <a name="recognize-emotions-sample-response"></a>Resposta de exemplo de Detecção de Emoções
Uma chamada bem-sucedida retorna uma matriz de entradas de face e suas classificações de emoção associadas, classificadas pelo tamanho do retângulo de face em ordem decrescente. Uma resposta vazia indica que nenhuma face foi detectada. Uma entrada de emoção contém os seguintes campos:
* faceRectangle - local do retângulo de face da imagem.
* classificações - classificações de Detecção de Emoções para cada face na imagem.

```json
application/json
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
