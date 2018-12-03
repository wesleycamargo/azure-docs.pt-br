---
title: 'Início Rápido: executar uma pesquisa com Python – API de Pesquisa na Web do Bing'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a fazer sua primeira chamada à API de Pesquisa na Web do Bing usando Python e receber uma resposta JSON.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: aahi
ms.openlocfilehash: 0f6f3991e01e4eb6919d958002ef6230a2570dbe
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309446"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Início Rápido: Usar Python para chamar a API de Pesquisa na Web do Bing  
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em **Pesquisar**.  Veja também [Preços dos Serviços Cognitivos - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Use este início rápido para fazer sua primeira chamada à API de Pesquisa na Web do Bing e receber uma resposta JSON em menos de 10 minutos.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

Veja também [Preços dos Serviços Cognitivos - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Este exemplo é executado como um Jupyter Notebook em [MyBinder](https://mybinder.org). Clique no selo de inicializar Associador:

[![Associador](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="define-variables"></a>Definir variáveis

Substitua o valor `subscription_key` por uma chave de assinatura válida da sua conta do Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Declare o ponto de extremidade da API de Pesquisa na Web do Bing. Caso encontre erros de autorização, verifique novamente esse valor em relação ao ponto de extremidade de pesquisa do Bing no painel do Azure.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Fique à vontade para personalizar a consulta de pesquisa substituindo o valor para `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Fazer uma solicitação

Este bloco usa a biblioteca `requests` para chamar a API de Pesquisa na Web do Bing e retornar os resultados como um objeto JSON. A chave de API é passada no dicionário `headers` e os parâmetros de consulta e termo de pesquisa são passados no dicionário `params`. Veja a documentação [API de Pesquisa na Web do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference) para obter uma lista completa de opções e parâmetros.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formatar e exibir a resposta

O objeto `search_results` inclui os resultados da pesquisa e metadados como consultas e as páginas relacionadas. Este código usa a biblioteca `IPython.display` para formatar e exibir a resposta no seu navegador.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Exemplo de código no GitHub

Se você quiser executar esse código localmente, o [exemplo completo está disponível no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única de pesquisa na Web do Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
