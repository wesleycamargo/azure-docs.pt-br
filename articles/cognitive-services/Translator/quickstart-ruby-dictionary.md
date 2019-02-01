---
title: 'Início Rápido: Procure palavras com o dicionário bilíngue, Ruby – API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você encontrará traduções alternativas e exemplos de termos em contexto usando a API de Tradução de Texto com Ruby.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: erhopf
ms.openlocfilehash: 76357db32ad676cc5246e7f9edfae238d33b0696
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461144"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-ruby"></a>Início Rápido: Procure palavras com o dicionário bilíngue Ruby

Neste início rápido, você encontrar detalhes de traduções alternativas possíveis para um termo, e também exemplos de uso dessas traduções alternativas usando a API de Tradução de Texto.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) ou posterior para executar esse código.

Para usar a API de Tradução de Texto, você também precisa de uma chave de assinatura. Veja [Como se inscrever para a API de Tradução de Texto](translator-text-how-to-signup.md).

## <a name="dictionary-lookup-request"></a>Solicitação de Pesquisa no Dicionário

O seguinte obtém traduções alternativas para uma palavra usando o método [Pesquisa no Dicionário](./reference/v3-0-dictionary-lookup.md).

1. Crie um novo projeto Ruby em seu editor de código favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/dictionary/lookup?api-version=3.0'

params = '&from=en&to=fr'

uri = URI (host + path + params)

text = 'great'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="dictionary-lookup-response"></a>Resposta da Pesquisa no Dicionário

Uma resposta bem-sucedida é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
[
  {
    "normalizedSource": "great",
    "displaySource": "great",
    "translations": [
      {
        "normalizedTarget": "grand",
        "displayTarget": "grand",
        "posTag": "ADJ",
        "confidence": 0.2783,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 34358
          },
          {
            "normalizedText": "big",
            "displayText": "big",
            "numExamples": 15,
            "frequencyCount": 21770
          },
...
        ]
      },
      {
        "normalizedTarget": "super",
        "displayTarget": "super",
        "posTag": "ADJ",
        "confidence": 0.1514,
        "prefixWord": "",
        "backTranslations": [
          {
            "normalizedText": "super",
            "displayText": "super",
            "numExamples": 15,
            "frequencyCount": 12023
          },
          {
            "normalizedText": "great",
            "displayText": "great",
            "numExamples": 15,
            "frequencyCount": 10931
          },
...
        ]
      },
...
    ]
  }
]
```

## <a name="dictionary-examples-request"></a>Solicitação Exemplos do Dicionário

O seguinte obtém exemplos contextuais de como usar um termo no dicionário usando o método [Exemplos do Dicionário](./reference/v3-0-dictionary-examples.md).

1. Crie um novo projeto Ruby em seu editor de código favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/dictionary/examples?api-version=3.0'

params = '&from=en&to=fr'

uri = URI (host + path + params)

text = 'great'
translation = 'formidable'

content = '[{"Text" : "' + text + '", "Translation" : "' + translation + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="dictionary-examples-response"></a>Resposta de Exemplos do Dicionário

Uma resposta bem-sucedida é retornada em JSON, conforme mostrado no seguinte exemplo:

```json
[
  {
    "normalizedSource": "great",
    "normalizedTarget": "formidable",
    "examples": [
      {
        "sourcePrefix": "You have a ",
        "sourceTerm": "great",
        "sourceSuffix": " expression there.",
        "targetPrefix": "Vous avez une expression ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
      {
        "sourcePrefix": "You played a ",
        "sourceTerm": "great",
        "sourceSuffix": " game today.",
        "targetPrefix": "Vous avez été ",
        "targetTerm": "formidable",
        "targetSuffix": "."
      },
...
    ]
  }
]
```

## <a name="next-steps"></a>Próximas etapas

Explore o exemplo de código para este início rápido e outros, incluindo tradução e transliteração, bem como outros projetos de Tradução de Texto de exemplo no GitHub.

> [!div class="nextstepaction"]
> [Explorar exemplos do Ruby no GitHub](https://aka.ms/TranslatorGitHub?type=&language=ruby)
