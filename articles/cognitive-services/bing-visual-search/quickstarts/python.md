---
title: 'Início Rápido: Obtenha insights de imagem usando a API REST da Pesquisa Visual do Bing e o Python'
titleSuffix: Azure Cognitive Services
description: Saiba como carregar uma imagem para a API da Pesquisa Visual do Bing e obter insights sobre ela.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 873da64592c2c2e925d8731d4b1154db95bed31d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863220"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Início Rápido: Sua primeira consulta de Pesquisa Visual do Bing em Python

Use este início rápido para fazer sua primeira chamada à API da Pesquisa Visual do Bing e exibir os resultados da pesquisa. Este aplicativo JavaScript simples carrega uma imagem para a API e exibe as informações retornadas sobre ela. Embora esse aplicativo seja escrito em JavaScript, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Ao carregar uma imagem local, os dados de formulário de POST precisam incluir o cabeçalho Content-Disposition. Seu parâmetro `name` precisa ser definido como "image" e o parâmetro `filename` pode ser definido como qualquer cadeia de caracteres. O conteúdo do formulário é o binário da imagem. O tamanho máximo de imagem que você pode fazer upload é 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicializar o aplicativo

1. Crie um arquivo Python em seu IDE ou editor favorito e adicione a seguinte instrução de importação.

    ```python
    import requests, json
    ```

2. Crie variáveis para a chave de assinatura, o ponto de extremidade e o caminho para a imagem que você está carregando.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Crie um objeto de dicionário para armazenar as informações de cabeçalho das solicitações. Associe a chave de assinatura à cadeia de caracteres `Ocp-Apim-Subscription-Key`, conforme mostrado abaixo.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Crie outro dicionário para conter a imagem, que será aberta e carregada quando você enviar a solicitação. 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analisar a resposta JSON

1. Crie um método chamado `print_json()` para obter a resposta da API e imprimir o JSON.

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Enviar a solicitação

1. Use `requests.post()` para enviar uma solicitação para a API da Pesquisa Visual do Bing. Inclua a cadeia de caracteres para o ponto de extremidade, o cabeçalho e as informações do arquivo. Imprimir `response.json()` com `print_json()`

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de Pesquisa Personalizada](../tutorial-bing-visual-search-single-page-app.md)
