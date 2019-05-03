---
title: 'Início Rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta e o Node. js'
description: Use a API de reconhecimento de tinta para iniciar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: febc6e72ed40541a230c606a3ec96a8bffef5036
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026372"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Início Rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta e o JavaScript

Use este guia de início rápido para começar a usar a API de reconhecimento de tinta em traços de tinta digital. Este aplicativo JavaScript envia uma solicitação de API que contém dados do traço de tinta formatada em JSON e exibe a resposta.

Embora esse aplicativo é escrito em Javascript e é executado no navegador da web, a API é um serviço web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamaria a API de um aplicativo de tinta digital. Neste início rápido envia dados do traço de tinta para o exemplo a seguir manuscrito em um arquivo JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte deste Início Rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Pré-requisitos

- Um navegador da Web
- Os dados de traço de tinta de exemplo para este início rápido podem ser encontrados no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. No seu IDE ou editor favorito, crie um novo `.html` arquivo. Em seguida, adicione HTML básico a ele para o código, que vamos adicionar mais tarde.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. Dentro de `<body>` marca, adicione o seguinte html:
    1. Duas áreas de texto para exibir o JSON de solicitação e resposta.
    2. Um botão para chamar o `recognizeInk()` função que será criada posteriormente.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Carregar o dados JSON de exemplo

1. Dentro de `<script>` marca, crie uma variável para o sampleJson. Em seguida, crie uma função JavaScript chamada `openFile()` que abre um Explorador de arquivos para que você possa selecionar o arquivo JSON. Quando o `Recognize ink` botão é clicado, ele chamar essa função e começar a ler o arquivo.
2. Use uma `FileReader` do objeto `onload()` função para processar o arquivo de forma assíncrona. 
    1. Substituir alguma `\n` ou `\r` caracteres no arquivo com uma cadeia de caracteres vazia. 
    2. Use `JSON.parse()` para converter o texto JSON válido
    3. Atualização de `request` caixa de texto no aplicativo. Use `JSON.stringify()` para formatar a cadeia de caracteres JSON. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Envie uma solicitação para a API de reconhecimento de tinta

1. Dentro de `<script>` marca, crie uma função chamada `recognizeInk()`. Essa função será mais tarde chamar a API e atualize a página com a resposta. Adicione o código das etapas a seguir nessa função. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Crie variáveis para a URL do ponto de extremidade, chave de assinatura e o exemplo de JSON. Em seguida, crie um `XMLHttpRequest` objeto para enviar a solicitação de API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Criar a função de retorno para o `XMLHttpRequest` objeto. Essa função analisar a resposta de API de uma solicitação bem-sucedida e exibi-lo no aplicativo. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Crie a função de erro para o objeto de solicitação. Essa função registra o erro no console. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Criar uma função para o objeto de solicitação `onreadystatechange` propriedade. Quando o estado de preparação do objeto request for alterado, as funções de retorno e de erro acima serão aplicadas.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Envie a solicitação de API. Adicione sua chave de assinatura para o `Ocp-Apim-Subscription-Key` cabeçalho e defina o `content-type` para `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)
