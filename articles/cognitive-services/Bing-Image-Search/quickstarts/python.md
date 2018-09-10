---
title: 'Início Rápido: enviar consultas de pesquisa usando a API REST para a API de Pesquisa de Imagem do Bing em Python'
description: Neste início rápido, você pode enviar consultas de pesquisa à API de Pesquisa do Bing para obter uma lista de imagens relevantes usando Python.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929787"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Início Rápido: enviar consultas de pesquisa usando a API REST e Python

A API de Pesquisa de Imagem do Bing proporciona uma experiência semelhante a Bing.com/Images, permitindo que você envie uma consulta de pesquisa de usuário ao Bing e obtenha uma lista de imagens relevantes.

Este passo a passo demonstra um exemplo simples de como chamar a API de Pesquisa de Imagens do Bing e o pós-processamento do objeto JSON resultante. Para obter mais informações, confira [Documentação da Pesquisa de Imagens do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

Você pode executar esse exemplo como um bloco de anotações do Jupyter em [MyBinder](https://mybinder.org) clicando no selo de inicialização do Binder: 

[![Associador](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>Executando o passo a passo
Para continuar com o passo a passo, defina `subscription_key` para sua chave de API para o serviço de API do Bing.


```python
subscription_key = None
assert subscription_key
```

Em seguida, verifique se o ponto de extremidade `search_url` está correto. Atualmente, apenas um ponto de extremidade é usado para APIs de pesquisa do Bing. Caso encontre erros de autorização, verifique esse valor em relação ao ponto de extremidade de pesquisa do Bing no painel do Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Definir `search_term` para procurar por imagens de animais.


```python
search_term = "puppies"
```

O bloco a seguir usa a biblioteca `requests` do Python para chamar as APIs de pesquisa do Bing e retornar os resultados como um objeto JSON. Observe que passamos a chave de API por meio do dicionário `headers` e o termo de pesquisa por meio do dicionário `params`. Para ver a lista completa de opções que podem ser usadas para filtrar os resultados da pesquisa, veja a documentação da [API REST](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

O objeto `search_results` contém as imagens reais junto com os metadados avançados, como consultas e páginas relacionadas. Por exemplo, a seguinte linha de código pode extrair a miniatura URLS para os primeiros 16 resultados.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Em seguida, podemos usar a `PIL` biblioteca para baixar as imagens em miniatura e `matplotlib` biblioteca para renderizá-los em uma grade de $ $4 \vezes 4.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de aplicativo de página única da Pesquisa de Imagem do Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Consulte também 

[Visão geral da Pesquisa de Imagem do Bing](../overview.md)  
[Experimente](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Obter uma chave de acesso de avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Referência da API de Pesquisa de Imagem do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
