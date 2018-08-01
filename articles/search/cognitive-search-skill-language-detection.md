---
title: Habilidade de pesquisa cognitiva de detecção de ioma (Azure Search) | Microsoft Docs
description: Avalia o texto não estruturado e para cada registro, retorna um identificador de idioma com uma pontuação que indica a intensidade da análise em um pipeline de enriquecimento Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 443ac895085053b7c4c876c3deecaa1943c9f506
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171051"
---
#   <a name="language-detection-cognitive-skill"></a>Habilidade cognitiva de detecção de idioma

Até 120 idiomas, a habilidade **Detecção de Idioma** detecta o idioma de texto de entrada e os relatórios de um código de idioma único para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da análise.

Esse recurso é especialmente útil quando você precisa fornecer o idioma do texto como entrada para outras habilidades (por exemplo, a habilidade de [Análise de Sentimento](cognitive-search-skill-sentiment.md) ou [Habilidade de Divisão de Texto](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser 50.000 caracteres conforme medido por `String.Length`. Se você precisar interromper o backup de seus dados antes de enviá-la ao analisador de sentimentos, use a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Entradas de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas     | DESCRIÇÃO |
|--------------------|-------------|
| text | O texto a ser analisado.|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída    | DESCRIÇÃO |
|--------------------|-------------|
| languageCode | O código de idioma ISO 6391 para o idioma identificado. Por exemplo, “in” |
| languagename | O nome do idioma. Por exemplo “inglês”. |
| para seu app&#39;s | Um valor entre 0 e 1. A probabilidade de que o idioma é identificado corretamente. A pontuação pode ser menor que 1, se a frase misturou idiomas.  |

##  <a name="sample-definition"></a>Definição de exemplo

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill ",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Saída de exemplo

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Casos de erro
Se o texto é expresso em um idioma sem suporte, um erro será gerado e nenhum identificador de idioma será retornado.

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
