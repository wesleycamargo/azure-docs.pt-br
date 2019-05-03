---
title: Habilidade de Detecção de Idioma da pesquisa cognitiva – Azure Search
description: Avalia o texto não estruturado e para cada registro, retorna um identificador de idioma com uma pontuação que indica a intensidade da análise em um pipeline de enriquecimento Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a076eee9818f294a8e5c4b10cebbcb9e5a55d80c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021843"
---
#   <a name="language-detection-cognitive-skill"></a>Habilidade cognitiva de detecção de idioma

O **detecção de idioma** habilidade detecta o idioma de texto de entrada e relata um código de idioma único para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da análise. Essa habilidade usa os modelos de machine learning fornecidos pela [Análise de Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) nos Serviços Cognitivos.

Esse recurso é especialmente útil quando você precisa fornecer o idioma do texto como entrada para outras habilidades (por exemplo, a habilidade de [Análise de Sentimento](cognitive-search-skill-sentiment.md) ou [Habilidade de Divisão de Texto](cognitive-search-skill-textsplit.md)).

Detecção de idioma aproveita a bibliotecas de processamento de linguagem natural do Bing, que excede o número de [suporte para idiomas e regiões](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) listados para análise de texto. A lista exata de idiomas não for publicada, mas inclui todos os idiomas falados amplamente, além de variantes, dialetos e alguns idiomas regionais e culturais. Se você tiver um conteúdo expressado em uma linguagem usada com menos frequência, você poderá [Experimente a API de detecção de idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) para ver se ele retorna um código. A resposta para idiomas que não pode ser detectado é `unknown`.

> [!NOTE]
> Como expandir escopo, aumentando a frequência de processamento, adicionando mais documentos, ou adicionar mais algoritmos de inteligência Artificial, você precisará [anexar a um recurso de serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem como parte do estágio de decodificação de documentos no Azure Search. Não há encargos para extração de texto em documentos.
>
> Execução de habilidades internas é cobrada existente [dos serviços Cognitivos pagamento medida que vá preços](https://azure.microsoft.com/pricing/details/cognitive-services/). Preços de extração de imagem é descrita na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


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
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
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
