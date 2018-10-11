---
title: 'Início Rápido: Criar uma consulta de pesquisa visual, Python – Pesquisa Visual do Bing'
titleSuffix: Azure Cognitive Services
description: Mostra como fazer upload de uma imagem para a API de Pesquisa Visual do Bing e obter insights sobre a imagem.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 16d3d0ddf77e37e32cc50961a3870b820ac2748e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884234"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Início Rápido: Sua primeira consulta da Pesquisa Visual do Bing em Python

A API de Pesquisa Visual do Bing retorna informações sobre uma imagem que você fornece. Você pode fornecer a imagem usando a URL da imagem, um token de insights ou fazendo upload de uma imagem. Para obter informações sobre essas opções, consulte [O que é a API de Pesquisa Visual do Bing?](../overview.md) Este artigo demonstra como fazer upload de uma imagem. Fazer upload de uma imagem pode ser útil em cenários móveis em que você tira uma foto de um ponto de referência conhecido e obtém informações sobre ele. Por exemplo, os insights podem incluir fatos secundários sobre o ponto de referência. 

Se você fizer upload de uma imagem local, inclua no corpo do POST os dados de formulário mostrados a seguir. Os dados de formulário precisam incluir o cabeçalho Content-Disposition. Seu parâmetro `name` precisa ser definido como "image" e o parâmetro `filename` pode ser definido como qualquer cadeia de caracteres. O conteúdo do formulário é o binário da imagem. O tamanho máximo de imagem que você pode fazer upload é 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Este artigo inclui um aplicativo de console simples que envia uma solicitação de API de Pesquisa Visual do Bing e exibe os resultados da pesquisa JSON. Embora este aplicativo seja escrito em Python, a API é um serviço Web RESTful compatível com qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa do [Python 3](https://www.python.org/) para executar esse código.

Para este guia de início rápido, você pode usar uma chave de assinatura de [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) ou uma chave de assinatura paga.

## <a name="running-the-walkthrough"></a>Executando o passo a passo

Para executar este aplicativo, siga estas etapas:

1. Crie um novo projeto Python no seu IDE ou editor favorito.
2. Crie um arquivo nomeado visualsearch.py e adicione o código mostrado neste guia de início rápido.
3. Substitua o valor `SUBSCRIPTION_KEY` pela sua chave de assinatura.
3. Substitua o valor `imagePath` pelo caminho da imagem da qual o upload será feito.
4. Execute o programa.



A seguir, é mostrado como enviar a mensagem usando dados de formulário de várias partes em Python.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>Próximas etapas

[Obter insights sobre uma imagem usando um token de insights](../use-insights-token.md)  
[Tutorial de upload de imagem da Pesquisa Visual do Bing](../tutorial-visual-search-image-upload.md)
[Tutorial de aplicativo de página única da Pesquisa Visual do Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Visão geral da Pesquisa Visual do Bing](../overview.md)  
[Experimente](https://aka.ms/bingvisualsearchtryforfree)  
[Obter uma chave de acesso de avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referência de API de Pesquisa Visual do Bing](https://aka.ms/bingvisualsearchreferencedoc)
