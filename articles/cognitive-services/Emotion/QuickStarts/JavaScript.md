---
title: 'Início Rápido: Reconhecer emoções em rostos em uma imagem – API de Detecção de Emoções, JavaScript'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de códigos para ajudar você a começar a usar a API de Detecção de Emoções com JavaScript.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 3c6e5b0d759aeb8aa0b06e359f1f48eaf6c44520
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208935"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Início rápido: Criar um aplicativo para reconhecer emoções em rostos em uma imagem.

> [!IMPORTANT]
> A API de Detecção de Emoções será preterida em 15 de fevereiro de 2019. A funcionalidade de Detecção de Emoções agora está disponível como parte da [API de Detecção Facial](https://docs.microsoft.com/azure/cognitive-services/face/). 

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a usar rapidamente o [método de Reconhecimento da API de Detecção de Emoções](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) com o JavaScript para reconhecer as emoções expressas por uma ou mais pessoas em uma imagem.

## <a name="prerequisite"></a>Pré-requisito
* Obtenha sua Chave de Assinatura gratuita [aqui](https://azure.microsoft.com/try/cognitive-services/), ou caso tenha uma Assinatura do Azure, crie um recurso da API de Detecção de Emoções e obtenha sua Chave de Assinatura e seu Ponto de Extremidade nele.

![Criar um recurso de API de Detecção de Emoções](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Solicitação de exemplo de reconhecimento de emoções no JavaScript

Copie o conteúdo a script e salve-o em um arquivo como `test.html`. Altere a `url` de solicitação para usar o local em que você obteve as chaves de assinatura e substitua o valor de "Ocp-Apim-Subscription-Key" por sua chave de assinatura válida. Elas podem ser encontradas no portal do Azure, nas seções Visão geral e Chaves do recurso de API de Detecção de Emoções, respectivamente.

![Ponto de extremidade de API](../Images/api-url.png)

![Chave de assinatura de API](../Images/keys.png)

Altere o corpo da solicitação para o local de uma imagem que você quer usar. Para executar o exemplo, arraste e solte o arquivo no navegador.

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSSample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>

<h2>Face Rectangle</h2>
<ul id="faceRectangle">
<!-- Will populate list with response content -->
</ul>

<h2>Emotions</h2>
<ul id="scores">
<!-- Will populate list with response content -->
</ul>

<body>

<script type="text/javascript">
    $(function() {
        // No query string parameters for this API call.
        var params = { };

        $.ajax({
            // NOTE: You must use the same location in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the
            //   URL below with "westcentralus".
            url: "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?" + $.param(params),
            beforeSend: function(xhrObj){
                // Request headers, also supports "application/octet-stream"
                xhrObj.setRequestHeader("Content-Type","application/json");

                // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","<your subscription key>");
            },
            type: "POST",
            // Request body
            data: '{"url": "<your image url>"}',
        }).done(function(data) {
            // Get face rectangle dimensions
            var faceRectangle = data[0].faceRectangle;
            var faceRectangleList = $('#faceRectangle');

            // Append to DOM
            for (var prop in faceRectangle) {
                faceRectangleList.append("<li> " + prop + ": " + faceRectangle[prop] + "</li>");
            }

            // Get emotion confidence scores
            var scores = data[0].scores;
            var scoresList = $('#scores');

            // Append to DOM
            for(var prop in scores) {
                scoresList.append("<li> " + prop + ": " + scores[prop] + "</li>")
            }
        }).fail(function(err) {
            alert("Error: " + JSON.stringify(err));
        });
    });
</script>
</body>
</html>
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
