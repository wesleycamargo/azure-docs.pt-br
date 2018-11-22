---
title: 'Início Rápido: API de Pesquisa de Notícias do Bing, Python'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Pesquisa de Notícias do Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: aahi
ms.openlocfilehash: 2696401b15ceca86c7e7b7d33e7e7f7887f4b7dc
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164322"
---
# <a name="quickstart-for-bing-news-search-api-with-python"></a>Início Rápido para a API de Pesquisa de Notícias do Bing com o Python
Este passo a passo demonstra um exemplo simples de como chamar a API de Pesquisa de Notícias do Bing e o pós-processamento do objeto JSON resultante. Para obter mais informações, confira [Documentação da Pesquisa de Notícias do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  

Execute este exemplo como um Jupyter Notebook em [MyBinder](https://mybinder.org) clicando na notificação de inicialização do Associador: 

[![Associador](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

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
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

Defina `search_term` para procurar artigos de notícias sobre a Microsoft.


```python
search_term = "Microsoft"
```

O bloco a seguir usa a biblioteca `requests` do Python para chamar as APIs de Pesquisa do Bing e retornar os resultados como um objeto JSON. Observe que passamos a chave de API por meio do dicionário `headers` e o termo de pesquisa por meio do dicionário `params`. Para ver a lista completa de opções que podem ser usadas para filtrar os resultados da pesquisa, veja a documentação da [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

O objeto `search_results` contém os novos artigos relevantes junto com metadados avançados. Por exemplo, a linha de código a seguir extrai as descrições dos artigos.


```python
descriptions = [article["description"] for article in search_results["value"]]
```

Essas descrições podem então ser renderizadas como uma tabela com a palavra-chave de pesquisa realçada em **negrito**.


```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc) for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Percorrendo notícias](paging-news.md)
> [Usando marcadores de decoração para realçar o texto](hit-highlighting.md)

## <a name="see-also"></a>Consulte também 

 [Pesquisando notícias na Web](search-the-web.md)  
 [Experimente](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)
