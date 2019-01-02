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
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 741710a9f2a9e505681401183f5f41be0695633b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308559"
---
#   <a name="language-detection-cognitive-skill"></a>Habilidade cognitiva de detecção de idioma

Até 120 idiomas, a habilidade **Detecção de Idioma** detecta o idioma de texto de entrada e os relatórios de um código de idioma único para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da análise.

Esse recurso é especialmente útil quando você precisa fornecer o idioma do texto como entrada para outras habilidades (por exemplo, a habilidade de [Análise de Sentimento](cognitive-search-skill-sentiment.md) ou [Habilidade de Divisão de Texto](cognitive-search-skill-textsplit.md)).

> [!NOTE]
> A partir de 21 de dezembro de 2018, você poderá associar um recurso de Serviços Cognitivos a um conjunto de habilidades do Azure Search. Isso nos permitirá começar a cobrar pela execução do conjunto de habilidades. Nessa data, também começaremos a cobrar pela extração de imagens como parte do estágio de decodificação de documentos. A extração de texto de documentos continuará sendo oferecida sem custo adicional.
>
> A execução das habilidades internas será cobrada conforme o [preço pago conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existente. O preço da extração de imagens será cobrado conforme o preço da versão prévia, descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Saiba [mais](cognitive-search-attach-cognitive-services.md).

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
