---
title: Início rápido do Python do SDK de Pesquisa de Notícias | Microsoft Docs
description: Configuração para o aplicativo de console do SDK de Pesquisa de Notícias.
titleSuffix: Azure News Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 6d212d1477ecf583a038e33e72aab3d60f6aa050
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364648"
---
# <a name="news-search-sdk-python-quickstart"></a>Guia de início rápido do Python do SDK da Pesquisa de Notícias

O SDK de Pesquisa de Notícias contém a funcionalidade da API REST para consultas na Web e resultados da análise. 

O [código-fonte para amostras do SDK da Pesquisa de Notícias do Bing em Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) está disponível no GitHub.

## <a name="application-dependencies"></a>Dependências de aplicativo
Se você ainda não tiver feito isso, instale o Python. O SDK é compatível com Python 2.7, 3.3, 3.4, 3.5 e 3.6.

A recomendação geral para o desenvolvimento em Python é usar um [ambiente virtual](https://docs.python.org/3/tutorial/venv.html). Instale e inicialize o ambiente virtual com o [módulo venv](https://pypi.python.org/pypi/virtualenv). Você deve instalar virtualenv para Python 2.7.
```
python -m venv mytestenv
```
Instale dependências de SDK de Pesquisa de Notícias do Bing:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Cliente de Pesquisa de Notícias
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em *Pesquisar*. Adicione importações:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Crie uma instância de `CognitiveServicesCredentials`. Instancie o cliente:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Pesquise resultados e imprima o primeiro resultado da página da Web:
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")

```
Pesquise com filtros para obter as notícias mais recentes sobre "Inteligência Artificial" com os parâmetros `freshness` e `sortBy`. Verifique o número de resultados e imprima `totalEstimatedMatches`, `name`, `url`, `description`, `published time` e `name of provider` do primeiro resultado do item de notícias.
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Pesquise notícias da categoria para filme e entretenimento na TV com pesquisa segura. Verifique o número de resultados e imprima `category`, `name`, `url`, `description`, `published time` e `name of provider` do primeiro resultado do item de notícias.
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
Pesquise tópicos populares de notícias no Bing.  Verifique o número de resultados e imprima `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` e `image Url` do primeiro resultado de notícias.
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>Próximas etapas

[Amostras do SDK do Python de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


