---
title: 'Início Rápido: solicitar e filtrar imagens usando o SDK em Python'
description: Neste início rápido, você solicita e filtra as imagens retornadas pela Pesquisa de Imagem do Bing com o uso de Python.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 4729f103bb9b50d4ff039907db8eb677f3dc290a
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "41936731"
---
 # <a name="quickstart-request-and-filter-images-using-the-sdk-and-python"></a>Início Rápido: solicitar e filtrar imagens usando o SDK e Python

O SDK de Pesquisa de Imagem do Bing contém a funcionalidade de API REST para consultas à Web e resultados de análise. 

O [código-fonte para exemplos de SDK da Pesquisa de Imagem do Bing do Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) está disponível no GitHub.

## <a name="application-dependencies"></a>Dependências de aplicativo
Se o Python ainda não estiver instalado, instale-o. O SDK é compatível com Python 2.7, 3.3, 3.4, 3.5 e 3.6.

A recomendação geral para o desenvolvimento em Python é usar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html). Instale e inicialize o ambiente virtual com o [ módulo venv](https://pypi.python.org/pypi/virtualenv). Você deve instalar virtualenv para Python 2.7.
```
python -m venv mytestenv
```
Instale as dependências de SDK de Pesquisa de Imagem do Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Cliente de Pesquisa de Imagem
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em *Pesquisar*. Adicione importações:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Crie uma instância do `CognitiveServicesCredentials` e instancie o cliente:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Pesquise imagens na consulta (Yosemite), filtrada por gifs animados e aspecto amplo. Verifique o número de resultados e imprima insightsToken, a URL de miniatura e URL da Web do primeiro resultado.
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
Pesquisar imagens para (Yosemite), filtradas por gifs animados e aspecto amplo.  Verifique o número de resultados.  Imprima `insightsToken`, `thumbnail url` e `web url` do primeiro resultado.
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

Obter resultados mais populares:
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>Próximas etapas

[Exemplos do SDK do Python dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


