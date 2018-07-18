---
title: Área de corte do SDK de pesquisa Visual do Bing resultados tutorial | Microsoft Docs
description: Como usar o SDK de pesquisa Visual do Bing para obter URLs das imagens semelhante para cortar a área da imagem carregada.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 9bc3c180f108025f442343d8c5356982a83826a6
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958396"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Tutorial: Área de corte da imagem do SDK de Pesquisa Visual do Bing e resultados
O SDK de pesquisa Visual inclui uma opção para selecionar uma área de uma imagem e localizar imagens online que são semelhantes da área de corte da imagem maior.  Este exemplo especifica uma área de corte mostrando uma pessoa de uma imagem que contém muitas pessoas.  O código envia a área de corte e o URL da imagem maior e retorna resultados que incluem URLs da Pesquisa do Bing e URLs de imagens semelhantes encontradas on-line.

## <a name="prerequisites"></a>pré-requisitos

Você vai precisar [Visual Studio 2017](https://www.visualstudio.com/downloads/) para fazer este código executar no Windows. (A Edição Comunitária gratuita funcionará.)

Você deve ter uma [Conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com APIs de Pesquisa do Bing. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para esse início rápido. É necessário ter a chave de acesso fornecida ao ativar a avaliação gratuita ou você poderá usar uma chave de assinatura paga no painel do Azure.

## <a name="application-dependencies"></a>Dependências de aplicativo
Para configurar uma aplicação de console usando o SDK de Pesquisa de Web Bing, navegue até Gerenciar Pacotes NuGet do Gerenciador de Soluções no Visual Studio. Adicione o pacote Microsoft.Azure.CognitiveServices.Search.VisualSearch.

Instalar o pacote NuGet Web Search SDK também instala dependências, incluindo:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Imagem e área de corte
A imagem a seguir mostra o time de liderança sênior Microsoft.  Usando o Visual Search SDK, carregamos uma área de recorte da imagem e localizamos outras imagens e páginas da Web que incluam a entidade na área selecionada da imagem maior.  Neste caso, a entidade é uma pessoa.

![Time de Liderança Sênior Microsoft](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Especificar a área de corte como ImageInfo em VisualSearchRequest
Este exemplo usa uma área de corte da imagem anterior que especifica as coordenadas superior esquerda e inferior direita por porcentagem da imagem inteira.  O código a seguir cria um objeto `ImageInfo` da área de corte e carrega o objeto `ImageInfo` em um `VisualSearchRequest`.  O objeto `ImageInfo` também inclui a URL de uma imagem online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Procure for imagens similares à área de corte
O `VisualSearchRequest` contém informação de área de corte sobre a imagem e sua URL.  O método `VisualSearchMethodAsync` consegue os resultados.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Pegue os dados de URL de ImageModuleAction
Resultados de Pesquisa Visual são objetos `ImageTag`.  Cada marca contém uma lista de objetos `ImageAction`.  Cada `ImageAction` contém um campo `Data` que é uma lista de valores que dependem do tipo de ação:

Você consegue ter vários tipos com o seguinte código:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
A aplicação complete retorna:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

Como mostrado na lista anterior, o `Entity` `ActionType` contém uma consulta de Pesquisa do Bing que retorna informação sobre uma pessoa reconhecida, lugar ou coisa.  Os tipos `TopicResults` e `ImageResults` contêm consultas para imagens relacionadas. As URLs na lista conectam a resultados de pesquisa do Bing.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>PagesIncluding ActionType URLs de imagens encontradas por Pesquisa Visual

Pegar as URLs de imagens reais requer uma conversão que lê um `ActionType` como `ImageModuleAction`, que contém um elemento `Data` com uma lista de valores.  Cada valor é a URL de uma imagem.  O seguinte lança o tipo de ação `PagesIncluding` para `ImageModuleAction` e lê os valores.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="complete-code"></a>Código completo

O código seguinte executa exemplos anteriores. Ele envia um binário de imagem no corpo da solicitação de postagem, junto com um objeto cropArea, e imprime os URLs de pesquisa do Bing para cada ActionType. Se o ActionType é PagesIncluding, o código pega os itens ImageObject em Dados ImageObject.  Os Dados contêm uma lista de valores que são as URLs de imagens em páginas web.  Copie e cope URls de Pesquisa Visual resultantes em navegadores para mostrar resultados. Copie e cope itens ContentUrl em navegadores para mostrar imagens.

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

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
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
[Resposta da Pesquisa Visual](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview#the-response)