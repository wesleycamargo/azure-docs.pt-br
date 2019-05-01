---
title: 'Início Rápido: Detectar anomalias em seus dados de série temporal usando a API de REST do Detector de anomalias e C# | Microsoft Docs'
description: Use a API do Detector de anomalias para detectar anormalidades em sua série de dados como um lote ou no fluxo de dados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: ffa42eb2247d163dd6a146ffb26c1f8cc5cf6d82
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922530"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Início Rápido: Detectar anomalias em seus dados de série temporal usando a API de REST do Detector de anomalias eC# 

Use este guia de início rápido para começar a usar os dois modos de detecção a API Detector de anomalias para detectar anomalias em seus dados de série temporal. Isso C# aplicativo envia duas solicitações de API que contém dados de série temporal formatada em JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON que contém o status de anomalias (e outros dados) para cada ponto de dados em dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalias do último ponto de dados | A resposta JSON que contém o status de anomalias (e outros dados) para o último ponto de dados em dados de série temporal.                                                                                                                                         |

 Embora esse aplicativo seja escrito em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Para instalar o newtonsoft. JSON como um pacote do NuGet no Visual studio:
        1. Clique com botão direito no **Gerenciador de soluções**
        2. Clique em **Gerenciar Pacotes NuGet...**
        3. Pesquise `Newtonsoft.Json` e instale o pacote
- Se você estiver usando Linux/MacOS, esse aplicativo pode ser executado usando [Mono](https://www.mono-project.com/).

- Pontos de dados de série de tempo que contém um JSON arquivo. Os dados de exemplo para este início rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. No Visual Studio, crie uma nova solução de console e adicione os seguintes pacotes. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo estão os URIs que você pode usar para detecção de anomalias. Eles serão anexados ao seu ponto de extremidade de serviço mais tarde para criar a API URLs de solicitação.

    |Método de detecção  |URI  |
    |---------|---------|
    |Detecção de lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no ponto de dados mais recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Criar uma nova função assíncrona chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e as informações de cabeçalho usando um `HttpClient` objeto. Certifique-se de adicionar sua chave de assinatura para o `Ocp-Apim-Subscription-Key` cabeçalho. Em seguida, crie um `StringContent` objeto para a solicitação.

3. Enviar a solicitação com `PostAsync()`e, em seguida, retornar a resposta.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Criar uma nova função chamada `detectAnomaliesBatch()`. Construa a solicitação e enviá-lo chamando o `Request()` função com seu ponto de extremidade, chave de assinatura, a URL para a detecção de anomalias de lote e os dados de série temporal.

2. Desserializar o objeto JSON e gravá-lo no console.

3. Se a resposta contém `code` campo, o código de erro e a mensagem de erro de impressão. 

4. Caso contrário, localize as posições de anomalias no conjunto de dados. A resposta `isAnomaly` campo contém uma matriz de valores boolianos, cada qual indicando se um ponto de dados é uma anomalia. Converter isso em uma matriz de cadeia de caracteres com o objeto de resposta `ToObject<bool[]>()` função. Iterar por meio da matriz e o índice de qualquer de impressão `true` valores. Esses valores correspondem ao índice de pontos de dados anormais, caso seja encontrado.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do último ponto de dados

1. Criar uma nova função chamada `detectAnomaliesLatest()`. Construa a solicitação e enviá-lo chamando o `Request()` função com seu ponto de extremidade, chave de assinatura, a URL para a detecção de anomalias ponto mais recente e os dados de série temporal.

2. Desserializar o objeto JSON e gravá-lo no console.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar seus dados de série temporal e enviar a solicitação

1. No método principal do seu aplicativo, carregar os dados de série de tempo JSON com `File.ReadAllText()`. 

2. Chame as funções de detecção de anomalias criadas acima. Use `System.Console.ReadKey()` para manter a janela do console aberta após executar o aplicativo.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Exemplo de resposta

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção de lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Resposta a detecção do ponto mais recente de exemplo](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
