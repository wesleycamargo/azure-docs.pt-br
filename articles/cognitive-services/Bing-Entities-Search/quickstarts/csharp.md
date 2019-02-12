---
title: 'Início Rápido: Enviar uma solicitação de pesquisa para a API REST da Pesquisa de Entidade do Bing usando o C#'
titlesuffix: Azure Cognitive Services
description: Use este Início Rápido para enviar solicitações para a API REST de Pesquisa de Entidade do Bing usando C# e receba uma resposta JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 63890d916f80fbfac7173abd0df9e559bcd0b76b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754924"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Início Rápido: Enviar uma solicitação de pesquisa para a API REST da Pesquisa de Entidade do Bing usando o C#

Use este início rápido para fazer sua primeira chamada à API de Pesquisa de Entidade do Bing e exibir a resposta JSON. Este aplicativo C# simples envia uma consulta de pesquisa de notícias para a API e exibe a resposta. O código-fonte desse aplicativo está disponível no [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Embora esse aplicativo seja escrito em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.


## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* A estrutura [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote do NuGet.
* Se você estiver usando Linux/MacOS, este aplicativo poderá ser executado usando [Mono](http://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. crie uma solução de console C# no Visual Studio. Em seguida, adicione os seguintes namespaces no arquivo de código principal.
    
    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Crie uma nova classe e adicione variáveis para o ponto de extremidade da API, sua chave de assinatura e a consulta que você deseja pesquisar.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Enviar uma solicitação e obter a resposta da API

1. Na classe, crie uma função chamada `Search()`. Crie um novo objeto `HttpClient` e adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`.

    1. Construa o URI da sua solicitação combinando o host e o caminho. Em seguida, adicione o mercado e codifique a consulta como URL.
    2. Aguarde `client.GetAsync()` para obter uma resposta HTTP e, em seguida, armazene a resposta json aguardando `ReadAsStringAsync()`.
    3. Imprima a cadeia de caracteres para o console.

    ```csharp
    async static void Search()
    {
        //...
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

        HttpResponseMessage response = await client.GetAsync(uri);

        string contentString = await response.Content.ReadAsStringAsync();
        Console.WriteLine(JsonPrettyPrint(contentString));
    }
    ```

2. No principal método do aplicativo, chame a função `Search()`.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>Resposta JSON de exemplo

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade do Bing?](../overview.md )
* [Referência da API de Pesquisa de Entidade do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
