---
title: Habilidade de pesquisa cognitiva do Sentiment (Azure Search) | Microsoft Docs
description: Extrai sentimento do texto em um pipeline de enriquecimento do Azure Search.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1ddbba5b881cd05a997cd24a9396d5b722376e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786755"
---
#   <a name="sentiment-cognitive-skill"></a>Habilidade cognitiva do Sentiment

A habilidade do **Sentiment** avalia o texto não estruturado ao longo de um conjunto negativo positivo e para cada registro, retorna uma pontuação numérica entre 0 e 1. Pontuações próximas de 1 indicam sentimento positivo e pontuações próximas de 0 indicam sentimento negativo.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limites de dados
O tamanho máximo de um registro deve ser 5000 caracteres conforme medido por `String.Length`. Se você precisar interromper o backup de seus dados antes de enviá-lo ao analisador de sentimentos, use a [habilidade Text Split](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parâmetros de habilidades

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do Parâmetro |                      |
|----------------|----------------------|
| defaultLanguageCode | (opcional) O código de idioma a ser aplicado a documentos que não especifica explicitamente o idioma. <br/> Consulte [Lista completa dos idiomas com suporte](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Entradas de habilidades 

| Nome de entrada | DESCRIÇÃO |
|--------------------|-------------|
| text | O texto a ser analisado.|
| languageCode  |  (opcional) Uma cadeia de caracteres que indica o idioma dos registros. Se este parâmetro não for especificado, o valor padrão é “en”. <br/>Consulte [Lista completa dos idiomas com suporte](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída | DESCRIÇÃO |
|--------------------|-------------|
| para seu app&#39;s | Um valor entre 0 e 1 que representa o sentimento do texto analisado. Valores próximos a 0 têm sentimento negativo, perto de 0,5, têm sentimento neutro, e valores próximo a 1 têm o sentimento positivo.|


##  <a name="sample-definition"></a>Definição de exemplo

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Saída de exemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Observações
Se estiver vazio, uma pontuação de sensibilidade não é retornada para os registros.

## <a name="error-cases"></a>Casos de erro
Se não há suporte para um idioma, será gerado um erro e nenhuma pontuação de sensibilidade é retornada.

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)