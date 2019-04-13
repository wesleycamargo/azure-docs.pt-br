---
title: 'Início Rápido: Detectar anomalias como um lote usando a API de REST do Detector de anomalias e o Python | Microsoft Docs'
description: Use a API do Detector de anomalias para detectar anormalidades em sua série de dados como um lote ou no fluxo de dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 6b4ddcadfe63f74d115c155354a276e45c6b53f9
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544493"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Início Rápido: Detectar anomalias em seus dados de série temporal usando a API de REST do Detector de anomalias e Python

Use este guia de início rápido para começar a usar os dois modos de detecção a API Detector de anomalias para detectar anomalias em seus dados de série temporal. Este aplicativo de Python envia duas solicitações de API que contém dados de série temporal formatada em JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON que contém o status de anomalias (e outros dados) para cada ponto de dados em dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalias do último ponto de dados | A resposta JSON que contém o status de anomalias (e outros dados) para o último ponto de dados em dados de série temporal.                                                                                                                                         |

 Embora esse aplicativo seja escrito em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.x ou 3.x](https://www.python.org/downloads/)

- O [biblioteca de solicitações](http://docs.python-requests.org) para python

- Pontos de dados de série de tempo que contém um JSON arquivo. Os dados de exemplo para este início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. Em seu editor de texto favorito ou IDE, crie um novo arquivo de python. Adicione as importações a seguir.

    ```python
    import requests
    import json
    ```

2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo estão os URIs que você pode usar para detecção de anomalias. Eles serão anexados ao seu ponto de extremidade de serviço mais tarde para criar a API URLs de solicitação.

    |Método de detecção  |URI  |
    |---------|---------|
    |Detecção de lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no ponto de dados mais recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Ler no arquivo de dados JSON, abri-lo e, em seguida, usando `json.load()`.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Criar uma nova função chamada `send_request()` que usa as variáveis criadas acima. Em seguida, siga estas etapas.

2. Crie um dicionário para os cabeçalhos de solicitação. Defina as `Content-Type` para `application/json`e adicione sua chave de assinatura para o `Ocp-Apim-Subscription-Key` cabeçalho.

3. Enviar a solicitação usando `requests.post()`. Combinar a URL de detecção de anomalias e de ponto de extremidade para a URL de solicitação completa e incluir cabeçalhos e os dados da solicitação json. E, em seguida, retornar a resposta.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Criar um método chamado `detect_batch()` para detectar anomalias em todo os dados como um lote. Chamar o `send_request()` método criado acima com seu ponto de extremidade, url, a chave de assinatura e dados json.

2. Chamar `json.dumps()` no resultado para formatá-lo e imprimi-lo no console.

3. Se a resposta contém `code` campo, o código de erro e a mensagem de erro de impressão.

4. Caso contrário, localize as posições de anomalias no conjunto de dados. A resposta `isAnomaly` campo contém um valor booliano relacionadas ao se um ponto de dados fornecido é uma anomalia. Iterar através da lista e imprimir o índice de qualquer `True` valores. Esses valores correspondem ao índice de pontos de dados anormais, caso seja encontrado.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do último ponto de dados

1. Criar um método chamado `detect_latest()` para determinar se o último ponto de dados da série de tempo é uma anomalia. Chamar o `send_request()` método acima com seu ponto de extremidade, url, a chave de assinatura e dados json. 

2. Chamar `json.dumps()` no resultado para formatá-lo e imprimi-lo no console.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar seus dados de série temporal e enviar a solicitação

1. Carregar os dados de série de tempo JSON abrindo um manipulador de arquivo e usando `json.load()` nele. Em seguida, chame métodos de detecção criados acima da anomalia.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Exemplo de resposta

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção de lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Resposta a detecção do ponto mais recente de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)