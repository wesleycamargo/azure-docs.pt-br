---
title: Início rápido do Python do SDK de Pesquisa na Web | Microsoft Docs
description: Configuração para aplicativo de console do SDK de Pesquisa na Web.
titleSuffix: Azure Cognitive Services Web search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 2a5fed58be863b882b827dbed73862bc690bab1e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364656"
---
# <a name="web-search-sdk-python-quickstart"></a>Início rápido do Python do SDK de Pesquisa na Web

O SDK de Pesquisa na Web do Bing contém a funcionalidade de API REST para consultas à Web e resultados da análise. 

O [código-fonte para exemplos do SDK de Pesquisa na Web do Bing do Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/web_search_samples.py) está disponível no GitHub.

## <a name="application-dependencies"></a>Dependências de aplicativo
Se o Python ainda não estiver instalado, instale-o. O SDK é compatível com Python 2.7, 3.3, 3.4, 3.5 e 3.6.

A recomendação geral para o desenvolvimento em Python é usar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html). Instale e inicialize o ambiente virtual com o [ módulo venv](https://pypi.python.org/pypi/virtualenv). Você deve instalar virtualenv para Python 2.7.
```
python -m venv mytestenv
```
Instale dependências de SDK de Pesquisa na Web do Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```
## <a name="web-search-client"></a>Cliente de pesquisa na Web
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em *Pesquisar*. Adicione importações e crie uma instância de `CognitiveServicesCredentials`:
```
from azure.cognitiveservices.search.websearch import WebSearchAPI
from azure.cognitiveservices.search.websearch.models import SafeSearch
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Em seguida, instancie o cliente:
```
client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Pesquise resultados e imprima o primeiro resultado da página da Web:
```
web_data = client.web.search(query="Yosemite")
print("\r\nSearched for Query# \" Yosemite \"")

# WebPages
if web_data.web_pages.value:

    print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

    first_web_page = web_data.web_pages.value[0]
    print("First web page name: {} ".format(first_web_page.name))
    print("First web page URL: {} ".format(first_web_page.url))

else:
    print("Didn't see any Web data..")
```
Imprima outros tipos de resultados, incluindo imagens, notícias e vídeos:
```
# Images
if web_data.images.value:

    print("\r\nImage Results#{}".format(len(web_data.images.value)))

    first_image = web_data.images.value[0]
    print("First Image name: {} ".format(first_image.name))
    print("First Image URL: {} ".format(first_image.url))

else:
    print("Didn't see any Image..")
        
# News
if web_data.news.value:

    print("\r\nNews Results#{}".format(len(web_data.news.value)))

    first_news = web_data.news.value[0]
    print("First News name: {} ".format(first_news.name))
    print("First News URL: {} ".format(first_news.url))

else:
    print("Didn't see any News..")
            
# Videos
if web_data.videos.value:

    print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

    first_video = web_data.videos.value[0]
    print("First Videos name: {} ".format(first_video.name))
    print("First Videos URL: {} ".format(first_video.url))

else:
    print("Didn't see any Videos..")

```
Pesquise (Melhores restaurantes em Seattle), verifique o número de resultados e imprima `name` e `URL` do primeiro resultado.
```
def web_results_with_count_and_offset(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
        print("\r\nSearched for Query# \" Best restaurants in Seattle \"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))```

```
Pesquise "xbox" com `response_filter` atribuído para `News`.  Imprima detalhes dos resultados de notícias.
```
def web_search_with_response_filter(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="xbox", response_filter=["News"])
        print("\r\nSearched for Query# \" xbox \" with response filters \"News\"")

        # News attribute since I filtered "News"
        if web_data.news.value:

            print("Webpage Results#{}".format(len(web_data.news.value)))

            first_web_page = web_data.news.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Pesquise com a consulta "Cataratas do Niágara" usando os parâmetros `answerCount` e `promote`. Imprima detalhes dos resultados.
```
def web_search_with_answer_count_promote_and_safe_search(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(
            query="Niagara Falls",
            answer_count=2,
            promote=["videos"],
            safe_search=SafeSearch.strict  # or directly "Strict"
        )
        print("\r\nSearched for Query# \" Niagara Falls\"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>Próximas etapas

[Exemplos do SDK do Python dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


