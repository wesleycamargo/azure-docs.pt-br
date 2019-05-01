---
title: 'Início Rápido: Detectar anomalias em seus dados de série temporal usando a API de REST do Detector de anomalias e o Java | Microsoft Docs'
description: Use a API do Detector de anomalias para detectar anormalidades em sua série de dados como um lote ou no fluxo de dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 1b52e578afb505c87c4084684e345b7aff6a4362
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922418"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Início Rápido: Detectar anomalias em seus dados de série temporal usando a API de REST do Detector de anomalias e Java

Use este guia de início rápido para começar a usar os dois modos de detecção a API Detector de anomalias para detectar anomalias em seus dados de série temporal. Esse aplicativo Java envia duas solicitações de API que contém dados de série temporal formatada em JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON que contém o status de anomalias (e outros dados) para cada ponto de dados em dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalias do último ponto de dados | A resposta JSON que contém o status de anomalias (e outros dados) para o último ponto de dados em dados de série temporal.                                                                                                                                         |

 Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- O [Java&trade; desenvolvimento Kit(JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.

- Essas bibliotecas de importação do repositório do Maven
    - [JSON em Java](https://mvnrepository.com/artifact/org.json/json) pacote
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) pacote

- Pontos de dados de série de tempo que contém um JSON arquivo. Os dados de exemplo para este início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. Crie um novo projeto Java em seu IDE ou editor favorito e importe as bibliotecas a seguir.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo estão os URIs que você pode usar para detecção de anomalias. Eles serão anexados ao seu ponto de extremidade de serviço mais tarde para criar a API URLs de solicitação.

    |Método de detecção  |URI  |
    |---------|---------|
    |Detecção de lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no ponto de dados mais recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Ler no arquivo de dados JSON

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Criar uma nova função chamada `sendRequest()` que usa as variáveis criadas acima. Em seguida, siga estas etapas.

2. Criar um `CloseableHttpClient` objeto que pode enviar solicitações para a API. Enviar a solicitação para um `HttpPost` objeto de solicitação, combinando o ponto de extremidade e uma URL do Detector de anomalias.

3. Use a solicitação `setHeader()` função para definir o `Content-Type` cabeçalho `application/json`e adicione sua chave de assinatura para o `Ocp-Apim-Subscription-Key` cabeçalho.

4. Use a solicitação `setEntity()` função aos dados a serem enviados.

5. Usar o cliente `execute()` função para enviar a solicitação e salve-o em um `CloseableHttpResponse` objeto.

6. Criar um `HttpEntity` objeto para armazenar o conteúdo da resposta. Obter o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorná-lo.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        request.setEntity(new StringEntity(requestData));
        try (CloseableHttpResponse response = client.execute(request)) {
            HttpEntity respEntity = response.getEntity();
            if (respEntity != null) {
                return EntityUtils.toString(respEntity, "utf-8");
            }
        } catch (Exception respEx) {
            respEx.printStackTrace();
        }
    } catch (IOException ex) {
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Criar um método chamado `detectAnomaliesBatch()` para detectar anomalias em todo os dados como um lote. Chamar o `sendRequest()` método criado acima com seu ponto de extremidade, url, a chave de assinatura e dados json. Obter o resultado e imprimi-lo no console.

2. Se a resposta contém `code` campo, o código de erro e a mensagem de erro de impressão.

3. Caso contrário, localize as posições de anomalias no conjunto de dados. A resposta `isAnomaly` campo contém um valor booliano relacionadas ao se um ponto de dados fornecido é uma anomalia. Obter a matriz JSON e iterar por meio dele, o índice de qualquer de impressão `true` valores. Esses valores correspondem ao índice de pontos de dados anormais, caso seja encontrado.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do último ponto de dados

* Criar um método chamado `detectAnomaliesLatest()` para detectar o status de anomalias do último ponto de dados no conjunto de dados. Chamar o `sendRequest()` método criado acima com seu ponto de extremidade, url, a chave de assinatura e dados json. Obter o resultado e imprimi-lo no console.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar seus dados de série temporal e enviar a solicitação

1. No método principal do seu aplicativo, no arquivo JSON que contém os dados que serão adicionados às solicitações de leitura.

2. Chame as funções de detecção de dois anomalias criadas acima.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Exemplo de resposta

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção de lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Resposta a detecção do ponto mais recente de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
