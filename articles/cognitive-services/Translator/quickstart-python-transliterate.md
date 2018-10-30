---
title: 'Início Rápido: Converter script de texto, Python - API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você converterá texto em um idioma de um script para outro usando a API de Tradução de Texto com Python.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: erhopf
ms.openlocfilehash: 2621e3ae165efe9f592400e3ad2782b396cf2a60
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647436"
---
# <a name="quickstart-transliterate-text-with-the-translator-text-rest-api-python"></a>Início Rápido: Transliterar texto com a API REST de Tradução de Texto (Python)

Neste início rápido, você converter texto em um idioma de um script para outro usando a API de Tradução de Texto.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Python 3.x](https://www.python.org/downloads/) para executar esse código.

Para usar a API de Tradução de Texto, você também precisa de uma chave de assinatura. Veja [Como se inscrever para a API de Tradução de Texto](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Solicitação de Transliterar

O seguinte converte texto em um idioma de um script em outro usando o método [Transliterar](./reference/v3-0-transliterate.md).

1. Crie um novo projeto Python em seu editor de código favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `subscriptionKey` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, uuid, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'

# Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
params = '&language=ja&fromScript=jpan&toScript=latn';

# Transliterate "good afternoon".
text = 'こんにちは'

def transliterate (content):

    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-type': 'application/json',
        'X-ClientTraceId': str(uuid.uuid4())
    }

    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path + params, content, headers)
    response = conn.getresponse ()
    return response.read ()

requestBody = [{
    'Text' : text,
}]
content = json.dumps(requestBody, ensure_ascii=False).encode('utf-8')
result = transliterate (content)

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
output = json.dumps(json.loads(result), indent=4, ensure_ascii=False)

print (output)
```

## <a name="transliterate-response"></a>Resposta de Transliterar

Uma resposta bem-sucedida é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Próximas etapas

Explore o exemplo de código para este início rápido e outros, incluindo tradução e identificação de idioma, bem como outros projetos de Tradução de Texto de exemplo no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos do Python no GitHub](https://aka.ms/TranslatorGitHub?type=&language=python)
