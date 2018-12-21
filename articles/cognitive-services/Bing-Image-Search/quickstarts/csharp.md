---
title: 'Início Rápido: Pesquisar imagens – API REST de Pesquisa de Imagem do Bing e C#'
titleSuffix: Azure Cognitive Services
description: Use este Início Rápido para enviar solicitações de pesquisa de imagens para a API REST de Pesquisa de Imagem do Bing usando C# e receba respostas JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 9/07/2018
ms.author: aahi
ms.openlocfilehash: 67dd91d7555ebd1e72cea168fc8b841bf4ed9175
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249508"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-c"></a>Início Rápido: Pesquisar imagens usando a API REST de Pesquisa de Imagem do Bing e C#

Use este início rápido para fazer sua primeira chamada à API de Pesquisa de Imagem do Bing e exibir um resultado de pesquisa da resposta JSON. Esse aplicativo C# simples envia uma consulta de pesquisa de imagem HTTP para a API e exibe a URL da primeira imagem retornada.

Embora esse aplicativo seja escrito em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

O código-fonte para esse exemplo está disponível [no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingImageSearchv7Quickstart.cs) com anotações de código e tratamentos de erro adicionais.

## <a name="prerequisites"></a>Pré-requisitos
* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* A estrutura [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote do NuGet.
* Se você estiver usando Linux/MacOS, este aplicativo poderá ser executado usando [Mono](http://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-a-project"></a>Criar e inicializar um projeto

1. crie uma nova solução de console chamada `BingSearchApisQuickStart` no Visual Studio. Em seguida, adicione os seguintes namespaces no arquivo de código principal.

    ```csharp
    using System;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    using Newtonsoft.Json.Linq;
    ```

2. Crie variáveis para o ponto de extremidade de API, sua chave de assinatura e o termo de pesquisa.

    ```csharp
    //...
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        // Replace the this string with your valid access key.
        const string subscriptionKey = "enter your key here";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";
        const string searchTerm = "tropical ocean";
    //...
    ```

## <a name="create-a-struct-to-format-the-bing-image-search-response"></a>Criar um struct para formatar a resposta da Pesquisa de Imagem do Bing

Defina um struct `SearchResult` para conter os resultados da pesquisa de imagem e as informações de cabeçalho do JSON.

```csharp
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        //...
            struct SearchResult
            {
                public String jsonResult;
                public Dictionary<String, String> relevantHeaders;
            }
//...
```

## <a name="create-a-method-to-send-search-requests"></a>Criar um método para enviar solicitações de pesquisa

Crie um método chamado `BingImageSearch` para realizar a chamada à API e definir o tipo de retorno ao struct `SearchResult` criado anteriormente.

```csharp
//...
namespace BingSearchApisQuickstart
{
    //...
    class Program
    {
        //...
        static SearchResult BingImageSearch(string searchTerm)
        {
        }
//...
```

## <a name="create-and-handle-an-image-search-request"></a>Criar e manipular uma solicitação de pesquisa de imagem

No método `BingImageSearch`, execute as seguintes etapas.

1. Construa o URI para a solicitação de pesquisa. Observe que o termo de pesquisa `toSearch` deve ser formatado antes de ser acrescentado à cadeia de caracteres.

    ```csharp
    static SearchResult BingImageSearch(string toSearch){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. Execute a solicitação da Web e obtenha a resposta como uma cadeia de caracteres JSON.

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. Crie o objeto de resultado de pesquisa e extraia os cabeçalhos HTTP do Bing. Em seguida, retorne `searchResult`.

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-and-view-the-response"></a>Processar e ver a resposta

1. No método principal, chame `BingImageSearch()` e armazene a resposta retornada. Em seguida, desserialize o JSON em um objeto.

    ```csharp
    SearchResult result = BingImageSearch(searchTerm);
    //deserialize the JSON response from the Bing Image Search API
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    ```

2. Obtenha a primeira imagem retornada de `jsonObj`e imprima o título e uma URL para a imagem.
    ```csharp
    var firstJsonObj = jsonObj["value"][0];
    Console.WriteLine("Title for the first image result: " + firstJsonObj["name"]+"\n");
    //After running the application, copy the output URL into a browser to see the image.
    Console.WriteLine("URL for the first image result: " + firstJsonObj["webSearchUrl"]+"\n");
    ```  

3. Certifique-se de remover sua chave de assinatura do código do aplicativo.

## <a name="json-response"></a>Resposta JSON

As respostas da API de Pesquisa de Imagem do Bing são retornadas como JSON. Este exemplo de resposta foi truncado para mostrar um único resultado.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Consulte também

* [O que é a Pesquisa de Imagem do Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Experimente uma demonstração interativa online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obtenha uma chave de acesso de Serviços Cognitivos grátis](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentação dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Referência da API de Pesquisa de Imagem do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
