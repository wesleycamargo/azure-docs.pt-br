---
title: Como usar a API de Localização de Anomalias com Java - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Obtenha informações e exemplos de códigos para ajudar você a começar a usar Java e a Detecção de Anomalias em Serviços Cognitivos.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: kefre
ms.openlocfilehash: 0f96ee13e6020bc69508f8ced45741fe7dec396a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60495044"
---
# <a name="use-the-anomaly-finder-api-with-java"></a>Usar a API de Localização de Anomalias com Java

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Este artigo fornece informações e exemplos de código para ajudar você a começar a usar a API de Detecção de Anomalias com Java para realizar tarefas de obtenção de resultado de detecção de anomalias para dados de série temporal.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-detection-api-using-java"></a>Obter pontos de anomalias com a API de Detecção de Anomalias usando Java

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exemplo de dados de série temporal

A seguir está um exemplo de pontos de dados de série temporal.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-java-example"></a>Exemplo de análise de dados e de obtenção de pontos de anomalias em Java

Para executar o exemplo, execute as seguintes etapas:
1. Crie um novo aplicativo de linha de comando.
2. Substitua a classe Main pelo código a seguir (mantenha todas as instruções `package`).
3. Substitua o valor `[YOUR_SUBSCRIPTION_KEY]` pela sua chave de assinatura válida.
4. Substitua `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` pelo exemplo ou por seus próprios pontos de dados.
5. Baixe essas bibliotecas global do repositório Maven para o diretório `lib` em seu projeto:
   * `org.apache.httpcomponents:httpclient:4.5.2`
6. Execute “Main”.

```java

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    public static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

    public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

    public static void main(String[] args) {
        final String content = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";

        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost request = new HttpPost(uriBase);

        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        try {
            StringEntity params = new StringEntity(content);
            request.setEntity(params);

            CloseableHttpResponse response = client.execute(request);
            try {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    System.out.println("----------");
                    System.out.println(response.getStatusLine());
                    System.out.println("Response content is :\n");
                    System.out.println(EntityUtils.toString(respEntity, "utf-8"));
                    System.out.println("----------");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            } finally {
                response.close();
            }

        } catch (Exception ex) {
            System.err.println("Exception on Anomaly Detection: " + ex.getMessage());
            ex.printStackTrace();
        } finally {
            try {
                client.close();
            } catch (Exception e) {
                System.err.println("Exception on closing HttpClient: " + e.getMessage());
                e.printStackTrace();
            }
        }
    }
}

```

### <a name="example-response"></a>Exemplo de resposta

Uma resposta com êxito é retornada em JSON. A seguir está a resposta de exemplo.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Aplicativo Java](../tutorials/java-tutorial.md)
