---
title: Início Rápido do C# do SDK da Pesquisa | Microsoft Docs
description: Configuração para aplicativo de console de C# do SDK da Pesquisa de Vídeo.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 05/16/2018
ms.author: v-gedod
ms.openlocfilehash: 65102862caf3ff2af0d4d7bfdb26d720d17c50ea
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37026293"
---
# <a name="visual-search-sdk-c-quickstart"></a>Início Rápido do C# do SDK da Pesquisa Visual

O SDK da Pesquisa Visual do Bing usa a funcionalidade da API REST para pedidos web e analisar resultados.
O [código-fonte para exemplos de SDK da Pesquisa Visual do Bing C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch) está disponível no Git Hub.

Cenários de código são documentados sob os seguintes cabeçalhos:
* [Cliente da Pesquisa Visual](#client)
* [Aplicativo do console completo](#complete)
* [Postagem de binários de imagem com cropArea](#binary-crop)
* [Parâmetro KnowledgeRequest](#knowledge-req)
* [Marcas, ações e actionType](#tags-actions)
* [Número de marcas, número de ações, e primeiro actionType](#num-tags-actions)

## <a name="prerequisites"></a>pré-requisitos
* Visual Studio 2017. Se necessário, você pode baixar versão gratuita da comunidade aqui: https://www.visualstudio.com/vs/community/.
* Uma chave API de Serviços Cognitivos é necessária para autenticar chamadas SDK. Inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=search-api-v7). A chave de avaliação é boa para sete dias com uma chamada por segundo. Para cenário de produção, [compre chave de acesso](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7). Consulte também [informações de preço](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/visual/).
* A habilidade de executar .NET núcleo SDK, aplicativos .net núcleo 1.1. Você consegue pegar CORE, Framework e Runtime aqui: https://www.microsoft.com/net/download/.

## <a name="application-dependencies"></a>Dependências de aplicativo

Para configurar uma aplicação de console usando o SDK de Pesquisa de Web Bing, navegue até a opção `Manage NuGet Packages` do Gerenciador de Soluções no Visual Studio.  Adicione o pacote `Microsoft.Azure.CognitiveServices.Search.VisualSearch`.

Instalar o [pacote NuGet Web Search SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VisualSearch/1.0) também instala dependências, incluindo:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

<a name="client"></a> 
## <a name="visual-search-client"></a>Cliente da Pesquisa Visual
Para criar uma instância do `VisualSearchAPI` cliente, adicionar usando diretivas:
```
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;

```
Em seguida, instancie o cliente:
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Use o cliente para procurar imagens:
```
 System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open;
 // The knowledgeRequest parameter is not required if an image binary is passed in the request body
 var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;

```
Analise os resultados das consultas anteriores:
```
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }

    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags.First();
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions.First();
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }

```

<a name="complete"></a> 
## <a name="complete-console-application"></a>Aplicativo do console completo

O aplicativo de console a seguir executa o código anterior, outros cenários e analisa os resultados:
```
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using System;
using System.IO;
using System.Linq;

namespace VisualSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            String subKey = "YOUR-SUBSCRIPTION-KEY";

            VisualSearchImageBinary(subKey);
            //VisualSearchImageBinaryWithCropArea(subKey);
            //VisualSearchUrlWithFilters(subKey);
            //VisualSearchInsightsTokenWithCropArea(subKey);
            //VisualSearchUrlWithJson(subKey);

            Console.WriteLine("\r\nAny key to quit...");
            Console.ReadKey();
        }


        // This will send an image binary in the body of the post request and print out the imageInsightsToken, 
        //  the number of tags, the number of actions, and the first actionType.

        public static void VisualSearchImageBinary(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                using (System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open))
                {
                    // The knowledgeRequest parameter is not required if an image binary is passed in the request body
                    var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
                    Console.WriteLine("\r\nVisual search request with binary of image");

                    if (visualSearchResults == null)
                    {
                        Console.WriteLine("No visual search result data.");
                    }
                    else
                    {
                        // Visual Search results
                        if (visualSearchResults.Image?.ImageInsightsToken != null)
                        {
                            Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find image insights token!");
                        }

                        // List of tags
                        if (visualSearchResults.Tags.Count > 0)
                        {
                            var firstTagResult = visualSearchResults.Tags.First();
                            Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                            // List of actions in first tag
                            if (firstTagResult.Actions.Count > 0)
                            {
                                var firstActionResult = firstTagResult.Actions.First();
                                Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                                Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                            }
                            else
                            {
                                Console.WriteLine("Couldn't find tag actions!");
                            }

                        }
                        else
                        {
                            Console.WriteLine("Couldn't find image tags!");
                        }
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
}

```

Os exemplos de pesquisa Bing demonstram diversos recursos do SDK.  Adicione as funções seguintes à classe `VisualSrchSDK` anteriormente definida.

<a name="binary-crop"></a>
## <a name="image-binary-post-with-croparea"></a>Postagem de binários de imagem com cropArea

O código a seguir envia uma imagem binária no corpo da solicitação post, juntamente com um objeto cropArea.  Imprime o imageInsightsToken, o número de marcas, o número de ações e o primeiro actionType.

```
        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                using (FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open))
                {
                    // The ImageInfo struct contains a crop area specifying a region to crop in the uploaded image
                    CropArea CropArea = new CropArea(top: (float)0.1, bottom: (float)0.5, left: (float)0.1, right: (float)0.9);
                    ImageInfo ImageInfo = new ImageInfo(cropArea: CropArea);
                    VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(imageInfo: ImageInfo);

                    // The knowledgeRequest here holds additional information about the image, which is passed in in binary form
                    var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: VisualSearchRequest).Result;
                    Console.WriteLine("\r\nVisual search request with binary of image with knowledgeRequest of CropArea");

                    if (visualSearchResults == null)
                    {
                        Console.WriteLine("No visual search result data.");
                    }
                    else
                    {
                        // Visual Search results
                        if (visualSearchResults.Image?.ImageInsightsToken != null)
                        {
                            Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find image insights token!");
                        }

                        // List of tags
                        if (visualSearchResults.Tags.Count > 0)
                        {
                            var firstTagResult = visualSearchResults.Tags.First();
                            Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                            // List of actions in first tag
                            if (firstTagResult.Actions.Count > 0)
                            {
                                var firstActionResult = firstTagResult.Actions.First();
                                Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                                Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                            }
                            else
                            {
                                Console.WriteLine("Couldn't find tag actions!");
                            }

                        }
                        else
                        {
                            Console.WriteLine("Couldn't find image tags!");
                        }
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
<a name="knowledge-req"></a>
## <a name="knowledgerequest-parameter"></a>Parâmetro KnowledgeRequest

O seguinte código envia um url de imagem no parâmetro `knowledgeRequest`, junto com um filtro \"site:www.bing.com\".  Então imprime o `imageInsightsToken`, o número de marcas, o número de ações, e o primeiro actionType.
```
        public static void VisualSearchUrlWithFilters(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via URL, in the ImageInfo object
                var ImageUrl = "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80";
                ImageInfo ImageInfo = new ImageInfo(url: ImageUrl);

                // Optional filters inside the knowledgeRequest will restrict similar products and images to certain domains
                Filters Filters = new Filters(site: "www.bing.com");
                KnowledgeRequest KnowledgeRequest = new KnowledgeRequest(filters: Filters);

                // An image binary is not necessary here, as the image is specified via URL
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(imageInfo: ImageInfo, knowledgeRequest: KnowledgeRequest);
                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with url of image and knowledgeRequest based on filters");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {
                        var firstTagResult = visualSearchResults.Tags.First();
                        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                        // List of actions in first tag
                        if (firstTagResult.Actions.Count > 0)
                        {
                            var firstActionResult = firstTagResult.Actions.First();
                            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find tag actions!");
                        }

                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
<a name="tags-actions"></a>
## <a name="tags-actions-and-actiontype"></a>Marcas, ações e actionType

O código seguinte envia um token de insights de imagem no parâmetro knowledgeRequest, junto com um objeto cropArea.  Imprime o imageInsightsToken, o número de marcas, o número de ações e o primeiro actionType.

```
        public static void VisualSearchInsightsTokenWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object
                var ImageInsightsToken = "bcid_113F29C079F18F385732D8046EC80145*ccid_oV/QcH95*mid_687689FAFA449B35BC11A1AE6CEAB6F9A9B53708*thid_R.113F29C079F18F385732D8046EC80145";

                // An optional crop area can be passed in to define a region of interest in the image
                CropArea CropArea = new CropArea(top: (float)0.1, bottom: (float)0.5, left: (float)0.1, right: (float)0.9);
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: ImageInsightsToken, cropArea: CropArea);

                // An image binary is not necessary here, as the image is specified via insights token
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(imageInfo: ImageInfo);
                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with ImageInsightsToken and knowledgeRequest based on imageInfo of cropArea");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {
                        var firstTagResult = visualSearchResults.Tags.First();
                        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                        // List of actions in first tag
                        if (firstTagResult.Actions.Count > 0)
                        {
                            var firstActionResult = firstTagResult.Actions.First();
                            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find tag actions!");
                        }

                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
```
<a name="num-tags-actions"></a>
## <a name="number-of-tags-number-of-actions-and-first-actiontype"></a>Número de marcas, número de ações, e primeiro actionType
O código seguinte envia um url de imagem no parâmetro knowledgeRequest, junto com um cropArea.  Então imprime o imageInsightsToken, o número de marcas, o número de ações e o primeiro actionType.
```
        public static void VisualSearchUrlWithJson(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {

                //The visual search request can be passed in as a JSON string
                //The image is specified via URL in the ImageInfo object, along with a crop area as shown below:
                
                //     "imageInfo": {
                //         "url": "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80",
                //     "cropArea": {
                //           "top": 0.1,
                //           "bottom": 0.5,
                //           "left": 0.1,
                //           "right": 0.9
                //         }
                //     },
                //     "knowledgeRequest": {
                //        "filters": {
                //            "site": "www.bing.com"
                //        }              
                //     }

                var VisualSearchRequestJSON = "{\"imageInfo\":{\"url\":\"https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80\",\"cropArea\":{\"top\":0.1,\"bottom\":0.5,\"left\":0.1,\"right\":0.9}},\"knowledgeRequest\":{\"filters\":{\"site\":\"www.bing.com\"}}}";

                // An image binary is not necessary here, as the image is specified by URL in JSON text
                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequestJSON).Result;
                Console.WriteLine("\r\nVisual search request with image url specified by JSON text");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {
                        var firstTagResult = visualSearchResults.Tags.First();
                        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");

                        // List of actions in first tag
                        if (firstTagResult.Actions.Count > 0)
                        {
                            var firstActionResult = firstTagResult.Actions.First();
                            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
                            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find tag actions!");
                        }

                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
```

## <a name="next-steps"></a>Próximas etapas

[Exemplos de SDK do .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).