---
title: 'Início Rápido: Pesquisa de Vídeo do Bing, Python'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: ccc27481289ffc686e3e480685ba421c762e3718
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161054"
---
# <a name="quickstart-bing-video-search-api-with-python"></a>Início Rápido: API de Pesquisa de Vídeo do Bing com o Python

Este passo a passo mostra como usar a API de Pesquisa de Vídeo do Bing, parte dos Serviços Cognitivos da Microsoft no Azure. Veja a [referência de API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) para obter detalhes técnicos sobre as APIs.

Você pode executar esse exemplo como um bloco de anotações do Jupyter em [MyBinder](https://mybinder.org) clicando no selo de inicialização do Binder: 

[![Associador](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Pré-requisitos
É necessário ter uma [conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com **APIs de Pesquisa do Bing**. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para esse início rápido. É necessário ter a chave de acesso fornecida ao ativar a avaliação gratuita ou você poderá usar uma chave de assinatura paga no painel do Azure.

## <a name="running-the-walkthrough"></a>Executando o passo a passo

Primeiro, defina `subscription_key` com a chave de API do serviço de API do Bing.


```python
subscription_key = None
assert subscription_key
```

Em seguida, verifique se o ponto de extremidade `search_url` está correto. Atualmente, apenas um ponto de extremidade é usado para APIs de pesquisa do Bing. Caso encontre erros de autorização, verifique esse valor em relação ao ponto de extremidade de pesquisa do Bing no painel do Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Definir `search_term` para procurar vídeos de gatos


```python
search_term = "kittens"
```

O bloco a seguir usa a biblioteca `requests` do Python para chamar as APIs de pesquisa do Bing e retornar os resultados como um objeto JSON. Observe que passamos a chave de API por meio do dicionário `headers` e o termo de pesquisa por meio do dicionário `params`. Para ver a lista completa de opções que podem ser usadas para filtrar os resultados da pesquisa, veja a documentação da [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

O objeto `search_results` contém os vídeos relevantes com os metadados ricos. Para exibir um dos vídeos, use sua `embedHtml` propriedade e insira em um `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Percorrendo vídeos](paging-videos.md)
> [Redimensionando e recortando imagens em miniatura](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>Consulte também 

 [Pesquisando vídeos na Web](search-the-web.md) [Experimente](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
