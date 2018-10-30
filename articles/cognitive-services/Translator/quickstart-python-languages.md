---
title: 'Início Rápido: Obter idiomas compatíveis, Python - API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você obterá uma lista dos idiomas compatíveis com tradução, transliteração e pesquisa em dicionário, além de exemplos que usam a API de Tradução de Texto com Java.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: erhopf
ms.openlocfilehash: b920b08d3bc5d4e340aa8ade6b26e5fe61208098
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49644718"
---
# <a name="quickstart-get-supported-languages-with-the-translator-text-rest-api-python"></a>Início Rápido: Obter idiomas compatíveis com a API REST de Tradução de Texto (Python)

Neste início rápido, você obtém uma lista dos idiomas com suporte para tradução, transliteração e pesquisa em dicionário, além de exemplos que usam a API de Tradução de Texto.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Python 3.x](https://www.python.org/downloads/) para executar esse código.

Para usar a API de Tradução de Texto, você também precisa de uma chave de assinatura. Veja [Como se inscrever para a API de Tradução de Texto](translator-text-how-to-signup.md).

## <a name="languages-request"></a>Solicitação de Idiomas

O código a seguir obtém uma lista dos idiomas compatíveis para tradução, transliteração e pesquisa no dicionário, além de exemplos usando o método [Idiomas](./reference/v3-0-languages.md).

1. Crie um novo projeto Python em seu editor de código favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'

output_path = 'output.txt'

def get_languages ():

    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}

    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_languages ()

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
json = json.dumps(json.loads(result), indent=4, ensure_ascii=False).encode('utf-8')

f = open(output_path, 'wb')
f.write (json)
f.close
```

## <a name="languages-response"></a>Resposta de Idiomas

Uma resposta bem-sucedida é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Explore o exemplo de código para este início rápido e outros, incluindo tradução e transliteração, bem como outros projetos de Tradução de Texto de exemplo no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos do Python no GitHub](https://aka.ms/TranslatorGitHub?type=&language=python)
