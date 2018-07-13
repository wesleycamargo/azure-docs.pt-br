---
title: Chamada e resposta - Início rápido do Python para Serviços Cognitivos do Azure, API de Pesquisa na Web do Bing | Microsoft Docs
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar a API de Pesquisa na Web do Bing nos Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363703"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>Chamada e resposta: a primeira consulta de Pesquisa na Web do Bing em Python

A API de Pesquisa na Web do Bing fornece uma experiência semelhante à Bing.com/Search, retornando resultados de pesquisa que o Bing determina como relevantes para a consulta do usuário. Os resultados podem incluir páginas da Web, imagens, vídeos, notícias e entidades, juntamente com as consultas de pesquisa relacionadas, correção ortográfica, fusos horários, conversão de unidade, traduções e cálculos. Os tipos de resultados obtidos são baseados em sua relevância e na camada das APIs de Pesquisa no Bing onde você deseja assinar.

Consulte a [referência de API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) para obter detalhes técnicos sobre as APIs.

Você pode executar esse exemplo como um bloco de anotações do Jupyter em [MyBinder](https://mybinder.org) clicando no selo de inicialização do Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>pré-requisitos
Você deve ter uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com **APIs de Pesquisa do Bing**. A [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) é suficiente para esse início rápido. É necessário ter a chave de acesso fornecida ao ativar a avaliação gratuita ou você poderá usar uma chave de assinatura paga no painel do Azure.

## <a name="running-the-walkthrough"></a>Executar o passo a passo

Definir `subscription_key` para a sua chave de API para o serviço de API do Bing.


```python
subscription_key = None
assert subscription_key
```

Em seguida, verifique se ponto de extremidade `search_url` está correto. Atualmente, apenas um ponto de extremidade é usado para APIs de pesquisa do Bing. Se você encontrar erros de autorização, verifique esse em relação ao ponto de extremidade de pesquisa do Bing no seu painel do Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Definir `search_term` para consultar o Bing para os Serviços Cognitivos da Microsoft.


```python
search_term = "Microsoft Cognitive Services"
```

O bloco a seguir usa a biblioteca `requests` do Python para chamar as APIs de pesquisa do Bing e retornar os resultados como um objeto JSON. Observar que passamos a chave de API por meio do dicionário `headers` e o termo de pesquisa através do dicionário `params`. Para ver a lista completa de opções que podem ser usadas para filtrar os resultados da pesquisa, consulte a documentação sobre [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

O objeto `search_results` contém os resultados da pesquisa junto com os metadados avançados, como consultas e páginas relacionadas. As linhas de código a seguir formatam as primeiras páginas retornadas pela consulta.


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de uma página de pesquisa na Web do Bing](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

[Visão geral de Pesquisa na Web do Bing](../overview.md)  
[Experimente](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Obter uma chave de acesso de avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Referência de API de Pesquisa na Web do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
