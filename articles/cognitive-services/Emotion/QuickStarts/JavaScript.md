---
title: Início Rápido da API de Detecção de Emoções no JavaScript | Microsoft Docs
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Detecção de Emoções com o JavaScript nos Serviços Cognitivos.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 2578b0212f9b4a6483402074d7c9eff73e51ca6b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363691"
---
# <a name="emotion-api-javascript-quick-start"></a>Início Rápido da API de Detecção de Emoções no JavaScript

> [!IMPORTANT]
> A versão prévia da API de vídeo terminará em 30 de outubro de 2017. Experimente a nova [versão prévia da API do Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) para extrair facilmente as informações dos vídeos e aprimorar as experiências de descoberta de conteúdo, tais como resultados da pesquisa através da detecção de palavras faladas, faces, personagens e emoções. [Saiba mais](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a usar rapidamente o [método de Reconhecimento da API de Detecção de Emoções](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) com o JavaScript para reconhecer as emoções expressas por uma ou mais pessoas em uma imagem.

## <a name="prerequisite"></a>Pré-requisito
* Obtenha sua Chave de Assinatura gratuita [aqui](https://azure.microsoft.com/try/cognitive-services/), ou caso tenha uma Assinatura do Azure, crie um recurso da API de Detecção de Emoções e obtenha sua Chave de Assinatura e seu Ponto de Extremidade nele.

![Criar um recurso de API de Detecção de Emoções](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Solicitação de exemplo de reconhecimento de emoções no JavaScript

Copie o conteúdo a script e salve-o em um arquivo como `test.html`. Altere a `url` de solicitação para usar o local em que você obteve as chaves de assinatura e substitua o valor de "Ocp-Apim-Subscription-Key" por sua chave de assinatura válida. Elas podem ser encontradas no portal do Azure, nas seções Visão geral e Chaves do recurso de API de Detecção de Emoções, respectivamente. 

![Ponto de extremidade de API](../Images/api-url.png)

![Chave de assinatura de API](../Images/keys.png)

e altere o corpo da solicitação para o local de uma imagem que você deseja usar. Para executar o exemplo, arraste e solte o arquivo no navegador.

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
