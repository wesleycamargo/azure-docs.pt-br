---
title: 'Início Rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta e o Java'
description: Use a API de reconhecimento de tinta para iniciar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 43dc388c9f24e3975d6efddbeec6e096bf062548
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026268"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Início Rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta e o Java

Use este guia de início rápido para começar a usar a API de reconhecimento de tinta em traços de tinta digital. Esse aplicativo Java envia uma solicitação de API que contém dados do traço de tinta formatada em JSON e obtém a resposta.

Embora esse aplicativo seja escrito em Java, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamaria a API de um aplicativo de tinta digital. Neste início rápido envia dados do traço de tinta para o exemplo a seguir manuscrito em um arquivo JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte deste Início Rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Pré-requisitos

- O [Java&trade; desenvolvimento Kit(JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior.

- Essas bibliotecas de importação do repositório do Maven
    - [JSON em Java](https://mvnrepository.com/artifact/org.json/json) pacote
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) pacote

- Os dados de traço de tinta de exemplo para este início rápido podem ser encontrados no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

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
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo está o URI que você pode usar para o reconhecimento de tinta. Ele será acrescentado ao seu ponto de extremidade de serviço mais tarde para criar a URL de solicitação de API.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Criar uma nova função chamada `sendRequest()` que usa as variáveis criadas acima. Em seguida, siga estas etapas.

2. Criar um `CloseableHttpClient` objeto que pode enviar solicitações para a API. Enviar a solicitação para um `HttpPut` objeto de solicitação, combinando o ponto de extremidade e a URL do reconhecedor de tinta.

3. Use a solicitação `setHeader()` função para definir o `Content-Type` cabeçalho `application/json`e adicione sua chave de assinatura para o `Ocp-Apim-Subscription-Key` cabeçalho.

4. Use a solicitação `setEntity()` função aos dados a serem enviados.   

5. Usar o cliente `execute()` função para enviar a solicitação e salve-o em um `CloseableHttpResponse` objeto. 

6. Criar um `HttpEntity` objeto para armazenar o conteúdo da resposta. Obter o conteúdo com `getEntity()`. Se a resposta não estiver vazia, retorná-lo.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
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
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Enviar uma solicitação de reconhecimento de tinta

Criar um método chamado `recognizeInk()` reconhecer os dados do traço de tinta. Chamar o `sendRequest()` método criado acima com seu ponto de extremidade, url, a chave de assinatura e dados json. Obter o resultado e imprimi-lo no console.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Carregar os dados de tinta digital e enviar a solicitação

1. No método principal do seu aplicativo, no arquivo JSON que contém os dados que serão adicionados às solicitações de leitura.

2. Chame a função de reconhecimento de tinta criada acima.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Execute o aplicativo e exibir a resposta

Execute o aplicativo. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON na [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API de reconhecimento de tinta funciona em um aplicativo de tinta digital, vejamos os seguintes aplicativos de exemplo no GitHub:
* [C#e Windows Universal Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C#e Foundation(WPF) de apresentação do Windows](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicativo de navegador da web do JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicativo móvel do Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicativo móvel SWIFT e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
