---
title: 'Início Rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta eC#'
description: Use a API de reconhecimento de tinta para iniciar a reconhecer traços de tinta digital.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: f03593292289cbc093832667505da2738c2b1633
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026281"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Início Rápido: Reconhecer tinta digital com a API de REST do reconhecedor de tinta eC#

Use este guia de início rápido para iniciar o envio de traços de tinta digital para a API de reconhecimento de tinta. Isso C# aplicativo envia uma solicitação de API que contém dados do traço de tinta formatada em JSON e obtém a resposta.

Embora esse aplicativo seja escrito em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Normalmente, você chamaria a API de um aplicativo de tinta digital. Neste início rápido envia dados do traço de tinta para o exemplo a seguir manuscrito em um arquivo JSON.

![uma imagem de texto manuscrito](../media/handwriting-sample.jpg)

O código-fonte deste Início Rápido pode ser encontrado no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Para instalar o newtonsoft. JSON como um pacote do NuGet no Visual studio:
        1. Clique com botão direito no **Gerenciador de soluções**
        2. Clique em **Gerenciar Pacotes NuGet...**
        3. Pesquise `Newtonsoft.Json` e instale o pacote
- Se você estiver usando Linux/MacOS, esse aplicativo pode ser executado usando [Mono](http://www.mono-project.com/).

- Os dados de traço de tinta de exemplo para este início rápido podem ser encontrados no [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


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
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Crie variáveis para sua chave de assinatura e seu ponto de extremidade. Abaixo está o URI que você pode usar para o reconhecimento de tinta. Ele será anexado ao seu ponto de extremidade de serviço mais tarde para criar a API do URl da solicitação.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Criar uma nova função assíncrona chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e as informações de cabeçalho usando um `HttpClient` objeto. Certifique-se de adicionar sua chave de assinatura para o `Ocp-Apim-Subscription-Key` cabeçalho. Em seguida, crie um `StringContent` objeto para a solicitação.
 
3. Enviar a solicitação com `PutAsync()`. Se a solicitação for bem-sucedida, retorne a resposta.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Enviar uma solicitação de reconhecimento de tinta

1. Criar uma nova função chamada `recognizeInk()`. Construa a solicitação e enviá-lo chamando o `Request()` função com seu ponto de extremidade, chave de assinatura, a URL para a API e os dados do traço de tinta digital.

2. Desserializar o objeto JSON e gravá-lo no console. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Carregue os dados de tinta digital

Criar uma função chamada `LoadJson()` para carregar o arquivo JSON de dados de tinta. Use uma `StreamReader` e `JsonTextReader` para criar um `JObject` e retorná-lo.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Enviar a solicitação de API

1. No método principal do seu aplicativo, carrega os dados JSON com a função criada acima. 

2. Chamar o `recognizeInk()` função criada acima. Use `System.Console.ReadKey()` para manter a janela do console aberta após executar o aplicativo.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Execute o aplicativo e exibir a resposta

Execute o aplicativo. Uma resposta bem-sucedida é retornada no formato JSON. Você também pode encontrar a resposta JSON na [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Para ver como a API de reconhecimento de tinta funciona em um aplicativo de tinta digital, vejamos os seguintes aplicativos de exemplo no GitHub:
* [C#e Windows Universal Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C#e Foundation(WPF) de apresentação do Windows](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplicativo de navegador da web do JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplicativo móvel do Java e Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplicativo móvel SWIFT e iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
