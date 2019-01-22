---
title: 'Início Rápido: Detectar rostos em uma imagem com a API REST do Azure e Java'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você usará a API de Detecção Facial do Azure com Java para detectar faces em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 2c035734d443eba01af6f167681ae289401dbcb4
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54212865"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Início Rápido: Detectar rostos em uma imagem usando a API REST e Java

Neste início rápido, você usará a API de Detecção Facial do Azure com Java para detectar faces humanas em uma imagem.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da API de Detecção Facial. É possível obter uma chave de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço API de Detecção Facial e obter sua chave.
- Qualquer Java IDE de sua preferência.

## <a name="create-the-java-project"></a>Criar o projeto Java

Crie um novo aplicativo Java de linha de comando em seu IDE e adicione uma classe **Main** com um método **main**. Depois, baixe estas bibliotecas globais do repositório Maven para o diretório `lib` de seu projeto:
* `org.apache.httpcomponents:httpclient:4.5.6`
* `org.apache.httpcomponents:httpcore:4.4.10`
* `org.json:json:20170516`
* `commons-logging:commons-logging:1.1.2`

## <a name="add-face-detection-code"></a>Adicionar código de detecção facial

Abra a classe main de seu projeto. Aqui, você adicionará o código necessário para carregar imagens e detectar faces.

### <a name="import-packages"></a>Importar pacotes

Adicione as seguintes instruções `import` à parte superior do arquivo.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Adicionar campos essenciais

Adicione os seguintes campos à classe **Main**. Esses dados especificam como se conectar ao serviço de Detecção Facial e onde obter os dados de entrada. Você precisará atualizar o campo `subscriptionKey` com o valor da sua chave de assinatura e, talvez, precise alterar a cadeia de caracteres `uriBase` para que contenha o identificador de região correto (confira os [documentos da API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter uma lista com todos os pontos de extremidade de região). Talvez você também queria definir o valor `imageWithFaces` para um caminho que aponte para um arquivo de imagem diferente.

O campo `faceAttributes` é simplesmente uma lista de certos tipos de atributos. Ele especificará quais informações devem ser recuperadas sobre as faces detectadas.

```Java
// Replace <Subscription Key> with your valid subscription key.
private static final String subscriptionKey = "<Subscription Key>";

// NOTE: You must use the same region in your REST call as you used to
// obtain your subscription keys. For example, if you obtained your
// subscription keys from westus, replace "westcentralus" in the URL
// below with "westus".
//
// Free trial subscription keys are generated in the "westus" region. If you
// use a free trial subscription key, you shouldn't need to change this region.
private static final String uriBase =
    "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";

private static final String imageWithFaces =
    "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

private static final String faceAttributes =
    "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Chamar a API REST de detecção facial

Adicione o seguinte método ao método **main**. Ele constrói uma chamada REST para a API de Detecção Facial para detectar informações sobre a face na imagem remota (a cadeia de caracteres `faceAttributes` especifica quais atributos de face recuperar). Em seguida, ele grava os dados de saída em uma cadeia de caracteres JSON.

```Java
HttpClient httpclient = HttpClientBuilder.create().build();

try
{
    URIBuilder builder = new URIBuilder(uriBase);

    // Request parameters. All of them are optional.
    builder.setParameter("returnFaceId", "true");
    builder.setParameter("returnFaceLandmarks", "false");
    builder.setParameter("returnFaceAttributes", faceAttributes);

    // Prepare the URI for the REST API call.
    URI uri = builder.build();
    HttpPost request = new HttpPost(uri);

    // Request headers.
    request.setHeader("Content-Type", "application/json");
    request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

    // Request body.
    StringEntity reqEntity = new StringEntity(imageWithFaces);
    request.setEntity(reqEntity);

    // Execute the REST API call and get the response entity.
    HttpResponse response = httpclient.execute(request);
    HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>Analisar a resposta JSON

Diretamente abaixo do código anterior, adicione o seguinte bloco, que converte os dados JSON retornados em um formato mais legível antes de imprimi-lo no console. Por fim, feche o bloco try-catch.

```Java
    if (entity != null)
    {
        // Format and display the JSON response.
        System.out.println("REST Response:\n");

        String jsonString = EntityUtils.toString(entity).trim();
        if (jsonString.charAt(0) == '[') {
            JSONArray jsonArray = new JSONArray(jsonString);
            System.out.println(jsonArray.toString(2));
        }
        else if (jsonString.charAt(0) == '{') {
            JSONObject jsonObject = new JSONObject(jsonString);
            System.out.println(jsonObject.toString(2));
        } else {
            System.out.println(jsonString);
        }
    }
}
catch (Exception e)
{
    // Display error message.
    System.out.println(e.getMessage());
}
```

## <a name="run-the-app"></a>Execute o aplicativo

Compile o código e execute-o. Uma resposta bem-sucedida exibirá dados de Detecção Facial em um formato JSON legível na janela do console. Por exemplo: 

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um aplicativo de console simples de Java que usa chamadas REST com a API de Detecção Facial do Azure para detectar faces em uma imagem e retornar seus atributos. Agora, saiba como aproveitar mais essa funcionalidade em um aplicativo para Android.

> [!div class="nextstepaction"]
> [Tutorial: Criar um aplicativo Android para detectar e enquadrar rostos](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
