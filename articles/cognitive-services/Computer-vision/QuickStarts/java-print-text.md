---
title: Início rápido de Java da API de Pesquisa Visual Computacional | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Neste início rápido, você extrai texto impresso de uma imagem usando a Pesquisa Visual Computacional com Java nos Serviços Cognitivos.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: a03652ab019730032ea02cfdc3ebc477379f8d03
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43768597"
---
# <a name="quickstart-extract-printed-text-ocr---rest-java"></a>Início Rápido: Extrair texto impresso (OCR) – REST, Java

Neste início rápido, você pode extrair texto impresso, também conhecido como OCR (reconhecimento óptico de caracteres) de uma imagem usando a Pesquisa Visual Computacional.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a Pesquisa Visual Computacional, você precisa de uma chave de assinatura. Veja [Obter chaves de assinatura](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="ocr-request"></a>Solicitação de OCR

Com o [método OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc), você pode detectar texto impresso em uma imagem e extrair os caracteres reconhecidos para um fluxo de caracteres utilizável por computador.

Para executar a amostra, siga estas etapas:

1. Crie um novo aplicativo de linha de comando.
1. Substitua a classe Main pelo código a seguir (mantenha todas as instruções `package`).
1. Substitua `<Subscription Key>` pela sua chave de assinatura válida.
1. Altere o valor `uriBase` para o local em que você adquiriu suas chaves de assinatura, se necessário.
1. Opcionalmente, altere o valor `imageToAnalyze` para outra imagem.
1. Baixe essas bibliotecas do Repositório Maven para o diretório `lib` em seu projeto:
   * `org.apache.httpcomponents:httpclient:4.5.5`
   * `org.apache.httpcomponents:httpcore:4.4.9`
   * `org.json:json:20180130`
1. Execute “Main”.

```java
// This sample uses the following libraries:
//  - Apache HTTP client (org.apache.httpcomponents:httpclient:4.5.5)
//  - Apache HTTP core (org.apache.httpcomponents:httpccore:4.4.9)
//  - JSON library (org.json:json:20180130).

import java.net.URI;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONObject;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // You must use the same region in your REST call as you used to get your
    // subscription keys. For example, if you got your subscription keys from
    // westus, replace "westcentralus" in the URI below with "westus".
    //
    // Free trial subscription keys are generated in the westcentralus region. If you
    // use a free trial subscription key, you shouldn't need to change this region.
    private static final String uriBase =
            "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr";

    private static final String imageToAnalyze =
        "https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/" +
            "Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder uriBuilder = new URIBuilder(uriBase);

            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation", "true");

            // Request parameters.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Make the REST API call and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="ocr-response"></a>Resposta de OCR

Uma resposta com êxito é retornada em JSON. Os resultados de OCR incluem o texto detectado e as caixas delimitadoras de regiões, linhas e palavras.

O programa deve produzir uma saída semelhante ao JSON a seguir:

```json
REST Response:

{
  "orientation": "Up",
  "regions": [{
    "boundingBox": "21,16,304,451",
    "lines": [
      {
        "boundingBox": "28,16,288,41",
        "words": [{
          "boundingBox": "28,16,288,41",
          "text": "NOTHING"
        }]
      },
      {
        "boundingBox": "27,66,283,52",
        "words": [{
          "boundingBox": "27,66,283,52",
          "text": "EXISTS"
        }]
      },
      {
        "boundingBox": "27,128,292,49",
        "words": [{
          "boundingBox": "27,128,292,49",
          "text": "EXCEPT"
        }]
      },
      {
        "boundingBox": "24,188,292,54",
        "words": [{
          "boundingBox": "24,188,292,54",
          "text": "ATOMS"
        }]
      },
      {
        "boundingBox": "22,253,297,32",
        "words": [
          {
            "boundingBox": "22,253,105,32",
            "text": "AND"
          },
          {
            "boundingBox": "144,253,175,32",
            "text": "EMPTY"
          }
        ]
      },
      {
        "boundingBox": "21,298,304,60",
        "words": [{
          "boundingBox": "21,298,304,60",
          "text": "SPACE."
        }]
      },
      {
        "boundingBox": "26,387,294,37",
        "words": [
          {
            "boundingBox": "26,387,210,37",
            "text": "Everything"
          },
          {
            "boundingBox": "249,389,71,27",
            "text": "else"
          }
        ]
      },
      {
        "boundingBox": "127,431,198,36",
        "words": [
          {
            "boundingBox": "127,431,31,29",
            "text": "is"
          },
          {
            "boundingBox": "172,431,153,36",
            "text": "opinion."
          }
        ]
      }
    ]
  }],
  "textAngle": 0,
  "language": "en"
}
```

## <a name="next-steps"></a>Próximas etapas

Explore um aplicativo Swing Java que use Pesquisa Visual Computacional para executar OCR (reconhecimento óptico de caracteres), crie miniaturas com recorte inteligente e detecte, categorize, marque e descreva recursos visuais, incluindo rostos, em uma imagem. Para experimentar rapidamente as APIs de Pesquisa Visual Computacional, tente o [Console de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tutorial de Java da API de Pesquisa Visual Computacional](../Tutorials/java-tutorial.md)
