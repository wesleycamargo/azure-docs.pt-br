---
title: 'Início Rápido: Reconhecer emoções em rostos em uma imagem – API de Detecção de Emoções, Ruby'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de códigos para ajudar você a começar a usar a API de Detecção de Emoções com Ruby.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 9ab0f4128e163ca887cf8f03425e33f3fbb6f01a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221973"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Início rápido: Criar um aplicativo para reconhecer emoções em rostos em uma imagem.

> [!IMPORTANT]
> A API de Detecção de Emoções será preterida em 15 de fevereiro de 2019. A funcionalidade de Detecção de Emoções agora está disponível como parte da [API de Detecção Facial](https://docs.microsoft.com/azure/cognitive-services/face/). 

Este artigo fornece informações e exemplos de código para ajudar você a começar a usar o [método de reconhecimento da API de Detecção de Emoções](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) com Ruby para reconhecer as emoções expressadas por uma ou mais pessoas em uma imagem.

## <a name="prerequisite"></a>Pré-requisito
* Obtenha sua chave de assinatura gratuita [aqui](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-ruby-example-request"></a>Solicitação de exemplo Ruby de Detecção de Emoções

Altere a URL do REST para usar o local em que você obteve as chaves de assinatura, substitua o valor de "Ocp-Apim-Subscription-Key" com a sua chave de assinatura válida e adicione uma URL para uma fotografia para a variável `body`.

```ruby
require 'net/http'

# NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
#   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
#   URL below with "westcentralus".
uri = URI('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize')
uri.query = URI.encode_www_form({
})

request = Net::HTTP::Post.new(uri.request_uri)
# Request headers
request['Content-Type'] = 'application/json'
# NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '13hc77781f7e4b19b5fcdd72a8df7156'
# Request body
request.body = "{\"url\":\"http://example.com/1.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body

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
