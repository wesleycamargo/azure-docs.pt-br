---
title: Início rápido de cURL da API de Detecção de Emoções | Microsoft Docs
description: Obtenha informações e exemplos de códigos para ajudar você a começar a usar a API de Detecção de Emoções com cURL em Serviços Cognitivos.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: a7ca2cac718797462bb4dc889b3f1361b252435e
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021091"
---
# <a name="emotion-api-curl-quick-start"></a>Início rápido de cURL da API de Detecção de Emoções

> [!IMPORTANT]
> A versão prévia da API de vídeo terminará em 30 de outubro de 2017. Experimente a nova [versão prévia da API do Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) para extrair facilmente as informações dos vídeos e aprimorar as experiências de descoberta de conteúdo, tais como resultados da pesquisa através da detecção de palavras faladas, faces, personagens e emoções. [Saiba mais](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

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
* classificação - classificações de Detecção de Emoções para cada face na imagem. 

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

