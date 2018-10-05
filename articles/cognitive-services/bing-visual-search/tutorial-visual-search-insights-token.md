---
title: 'Tutorial: ImageInsightsToken – Pesquisa Visual do Bing'
titlesuffix: Azure Cognitive Services
description: Como usar o SDK de pesquisa Visual do Bing para obter URLs das imagens especificadas pelo ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: bda4bdeea019d8cf3ae677d5eaf81e631ca38d16
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222566"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Tutorial: SDK de Pesquisa Visual Bing ImageInsightsToken e resultados
O SDK de Pesquisa Visual inclui uma opção para encontrar imagens online de uma pesquisa anterior que retorna em `ImageInsightsToken`.  Este exemplo obtém um `ImageInsightsToken` e usa o token em uma pesquisa subsequente.  O código envia o `ImageInsightsToken` para o Bing e retorna os resultados que incluem as URLs de Pesquisa do Bing e URLs de imagens semelhantes encontradas online.

## <a name="prerequisites"></a>Pré-requisitos
Visual Studio 2017. Se necessário, você pode baixar versão gratuita da comunidade aqui: https://www.visualstudio.com/vs/community/.
Uma chave API de Serviços Cognitivos é necessária para autenticar chamadas SDK. Inscreva-se em uma avaliação gratuita. A chave de avaliação é boa para sete dias com uma chamada por segundo. Para cenários de produção, compre uma chave de acesso. Consulte também as informações de preço.
A habilidade de executar .NET núcleo SDK, aplicativos .net núcleo 1.1. Você consegue pegar CORE, Framework e Runtime aqui: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Dependências de aplicativo
Para configurar uma aplicação de console usando o SDK de Pesquisa de Web Bing, navegue até Gerenciar Pacotes NuGet do Gerenciador de Soluções no Visual Studio. Adicionar:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Pacotes Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage.

Instalar o pacote NuGet Web Search SDK também instala dependências, incluindo:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Obter o ImageInsightsToken da Pesquisa de Imagem
Este exemplo utiliza um `ImageInsightsToken` obtido pelo método a seguir.  Para obter informações sobre essa chamada, consulte[Pesquisa de Imamgem SDK C# quickstart](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

O código procura resultados em uma consulta para 'Canadian Rockies' e obtém um ImageInsightsToken. Imprime o primeiro símbolo de insights da imagem, o URL da miniatura e o URL do conteúdo da imagem.  O método retorna o `ImageInsightsToken` para uso em uma solicitação subsequente da pesquisa visual.

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Especifique o ImageInsightsToken para solicitação do Visual Search
Este exemplo usa o token de insights retornado pelo método anterior. O código a seguir cria um `ImageInfo` objeto de `ImageInsightsToken` e carrega o objeto ImageInfo em `VisualSearchRequest`. Especifique `ImageInsightsToken` em um `ImageInfo` para o `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Use o Visual Search para encontrar imagens de um ImageInsightsToken
As `VisualSearchRequest` contêm informações sobre a imagem para pesquisar pelo `ImageInfo` objeto.  O método `VisualSearchMethodAsync` consegue os resultados.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pegue os dados de URL de ImageModuleAction
Resultados de Pesquisa Visual são objetos `ImageTag`.  Cada etiqueta contém uma lista de `ImageAction` objetos.  Cada `ImageAction` contém um campo `Data` que é uma lista de valores que dependem do tipo de ação:

Você consegue ter vários tipos com o seguinte código:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
A aplicação completa retorna:

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

Conforme exibido na lista anterior, os tipos `TopicResults` e `ImageResults` contêm consultas para as imagens relacionadas. As URLs na lista vinculam os resultados de pesquisa do Bing.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>URLs PagesIncluding ActionType e imagens encontradas pelo Visual Search.

Obter as URLs de imagem real requer que uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um `Data` elemento com uma lista de valores.  Cada valor é a URL de uma imagem.  As conversões a seguir `PagesIncluding` tipo de ação para`ImageModuleAction` e lê os valores.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Pra obter informações sobre esses tipos de dados, consulte [Imagens - Pesquisa Visual](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).
## <a name="complete-code"></a>Código completo

O código seguinte executa exemplos anteriores. Envia o `ImageInsightsToken` em uma solicitação de publicação. Em seguida, imprime as URLs de pesquisa do Bing para cada ActionType. Se o ActionType é `PagesIncluding`, o código obtém `ImageObject` itens em `Data`.  O `Data` contém uma lista de valores que são as URLs de imagens em páginas web.  Copie e cole URls de Pesquisa Visual resultantes em navegadores para mostrar resultados. Copie e cole itens ContentUrl em navegadores para mostrar imagens.

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    if (visualSearchResults.Tags.Count > 0)
                    {
                        // List of tags
                        foreach (ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            {
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                                    {
                                        Console.WriteLine("ContentURL: " + o.ContentUrl);
                                    }
                                }
                            }
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
    }
}

```
## <a name="next-steps"></a>Próximas etapas
[Resposta de Pesquisa Visual](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
