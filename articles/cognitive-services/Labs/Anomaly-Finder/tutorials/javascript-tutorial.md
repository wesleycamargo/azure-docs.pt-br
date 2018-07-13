---
title: Aplicativo Javascript de Detecção de Anomalias - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Explore um aplicativo Web Javascript que use a API de Detecção de Anomalias nos Serviços Cognitivos da Microsoft. Envie os pontos de dados originais para a API e obtenha os pontos de anomalias e o valor esperado.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364003"
---
# <a name="anomaly-detection-javascript-application"></a>Aplicativo do Javascript de detecção de Anomalias

Explore um aplicativo Web que use a API REST de Detecção de Anomalias para detectar uma anomalia. O exemplo envia os dados de série temporal para a API de Detecção de Anomalias com sua chave de assinatura, em seguida, obtém todos os pontos de anomalias e o valor esperado para cada ponto de dados da API.

## <a name="prerequisites"></a>pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Este tutorial foi desenvolvido usando um editor de texto simples.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Inscrever-se na detecção de anomalias e obter uma chave de assinatura 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Obter e usar o exemplo

Este tutorial fornece dois cenários para Detecção de Anomalias de dados de série temporal. Vamos começar.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Baixar o projeto de tutorial

Clone o [Tutorial de Detecção de Anomalias JavaScript dos Serviços Cognitivos](https://github.com/MicrosoftAnomalyDetection/javascript-sample) ou baixe o arquivo .zip e extraia-o para um diretório vazio.

<a name="Step2"></a>
### <a name="run-the-example"></a>Executar o exemplo

Há dois cenários em que você pode testar o exemplo.
1. Coloque sua **chave de assinatura** no campo de chave de assinatura na função detect em anomalydetection.html.
2. Coloque o ponto de extremidade de API de Detecção de Anomalias e verificar se você está usando a região correta na região de assinatura.
3. Abra o arquivo **anomalydetection.html** em um navegador da Web.

**Cenário 1 Detectar dados da série temporal semanal**
1. No campo Período, período de entrada **7**. 
2. Substitua os dados de exemplo por seus pontos de dados de série temporal semanal (Json) no campo Pontos ou use os dados de exemplo diretamente.
3. Clique no botão Detecção de Anomalias e verifique se o resultado na caixa de texto Resposta à direita.

**Cenário 2 Detectar os dados de série temporal sem um período**
1. Deixe o período vazio no campo Período, suponha que você não saiba o período da série temporal.
2. Usando os mesmos dados de série temporal do cenário 1.
3. Clique no botão de Detecção de Anomalias e verifique o campo Período na caixa de texto Resposta à direita.

<a name="Step3"></a>
### <a name="read-the-result"></a>Ler o resultado

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Analisar e aprender

Agora você pode obter um aplicativo em execução. Vamos analisar como o aplicativo de exemplo se integra com a tecnologia de Serviços Cognitivos. Esta etapa facilitará continuar aproveitando este aplicativo ou desenvolver seu próprio aplicativo usando a Detecção de Anomalias da Microsoft.
Este aplicativo de exemplo utiliza o ponto de extremidade da API Restful de Detecção de Anomalias.
Analisar como a API Restful é usada no aplicativo de exemplo, vamos examinar um trecho de código de anomalydetection.html.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
