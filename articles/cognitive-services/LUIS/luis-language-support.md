---
title: Suporte de idioma
titleSuffix: Azure Cognitive Services
description: LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de idioma. Verifique se os recursos em que você está interessado têm suporte na cultura do idioma de destino. Um aplicativo LUIS é específico à cultura e não pode ser alterado após a definição.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/19/2019
ms.author: diberry
ms.openlocfilehash: 735835d16eb14c3847f36ecb6f46c08c0a8928ef
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339509"
---
# <a name="language-and-region-support-for-luis"></a>Suporte de idioma e região para o LUIS

LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de idioma. Verifique se os recursos em que você está interessado têm suporte na cultura do idioma de destino. Um aplicativo LUIS é específico à cultura e não pode ser alterado após a definição.

## <a name="multi-language-luis-apps"></a>Aplicativos LUIS com vários idiomas

Se você precisar de um aplicativo de cliente LUIS com vários idiomas, como um chatbot, terá algumas opções. Se o LUIS der suporte a todos os idiomas, desenvolva um aplicativo LUIS para cada idioma. Cada aplicativo LUIS tem uma ID de aplicativo exclusiva e um log de ponto de extremidade. Se você precisa fornecer o reconhecimento de idioma para um idioma ao qual o LUIS não oferece suporte, use a [API de Tradução da Microsoft](../Translator/translator-info-overview.md) para traduzir a declaração para um idioma com suporte, envie a declaração para o ponto de extremidade do LUIS e receba as pontuações resultantes.

## <a name="languages-supported"></a>Idiomas compatíveis

O LUIS compreende declarações nos seguintes idiomas:

| Linguagem |Localidade  |  Domínio predefinido | Entidade predefinida | Recomendações de lista de frase | \**[Análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentimento e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano |`en-US` | ✔ | ✔  |✔|✔|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandês |`nl-NL` |-|  -   |-|✔|
| Francês (França) |`fr-FR` |-| ✔ |✔ |✔|
| Francês (Canadá) |`fr-CA` |-|   -   |-|✔|
| Alemão |`de-DE` |-| ✔ |✔ |✔|
| Italiano |`it-IT` |-| ✔ |✔|✔|
| *[Japonês](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Somente frase principal|
| Coreano |`ko-KR` |-|   -   |-|Somente frase principal|
| Português (Brasil) |`pt-BR` |-| ✔ |✔ |nem todas as subculturas|
| Espanhol (Espanha) |`es-ES` |-| ✔ |✔|✔|
| Espanhol (México)|`es-MX` |-|  -   |✔|✔|
| Turco | `tr-TR` |-|-|-|Sentimento, somente|


O suporte aos idiomas varia para [entidades predefinidas](luis-reference-prebuilt-entities.md) e [domínios predefinidos](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>*Notas de suporte ao chinês

 - Na cultura `zh-cn`, o LUIS espera o conjunto de caracteres de chinês simplificado em vez do conjunto de caracteres tradicional.
 - Os nomes de intenções, entidades, recursos e expressões regulares podem estar em caracteres chineses ou romanos.
 - Consulte a [referência de domínios predefinidos](luis-reference-prebuilt-domains.md) para obter informações no qual os domínios predefinidos têm suporte no `zh-cn` cultura.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>*Notas de suporte ao japonês

 - Como o LUIS não fornece análise sintática e não compreenderá a diferença entre Keigo e japonês informal, será necessário incorporar os diferentes níveis de formalidade como exemplos de treinamento para os seus aplicativos.
     - でございます não é o mesmo que です.
     - です não é o mesmo que だ.

### <a name="text-analytics-support-notes"></a>\*\*Notas de suporte a análises de texto
A análise de texto inclui análise de sentimento e entidade pré-compilada keyPhrase. Só há suporte para subculturas em português: `pt-PT` e `pt-BR`. Todas as outras culturas têm suporte no nível de cultura principal. Saiba mais sobre os [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) da Análise de Texto.

### <a name="speech-api-supported-languages"></a>Idiomas com suporte da API de Fala
Veja [Idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) de Fala para idiomas no modo de ditado de Fala.

### <a name="bing-spell-check-supported-languages"></a>Idiomas com suporte para Verificação Ortográfica do Bing
Veja [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) da Verificação Ortográfica do Bing para obter uma lista dos idiomas com suporte e status.

## <a name="rare-or-foreign-words-in-an-application"></a>Palavras raras ou estrangeiras em um aplicativo
Na cultura`en-us`, o LUIS aprende a distinguir mais palavras em inglês, incluindo gírias. Na cultura `zh-cn`, o LUIS aprende distinguir a maioria dos caracteres chineses. Se você usar uma palavra rara em `en-us` ou caractere em `zh-cn`, e você verá que o LUIS não parece capaz de distinguir essa palavra ou caractere, você pode adicionar essa palavra ou caractere a um [recurso de lista de frases](luis-how-to-add-features.md). Por exemplo, as palavras fora da cultura do aplicativo, ou seja, as palavras estrangeiras, devem ser adicionadas a um recurso de lista de frases. Essa lista de frases deve ser marcada como não intercambiável, para indicar que o conjunto de palavras raras forma uma classe a qual o LUIS deve aprender a reconhecer, mas que não são sinônimos nem intercambiáveis.

### <a name="hybrid-languages"></a>Idiomas híbridos
Os idiomas híbridos combinam palavras de duas culturas, como inglês e chinês. Não há suporte para esses idiomas no LUIS, pois um aplicativo tem base em uma única cultura.

## <a name="tokenization"></a>Geração de tokens
Para executar o aprendizado de máquina, o LUIS divide uma declaração em [tokens](luis-glossary.md#token) com base na cultura.

|Linguagem|  cada espaço ou caractere especial | nível do caractere|palavras compostas|[Entidade indexada retornada](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chinês||✔||✔|
|Holandês|||✔|✔|
|Inglês (en-us)|✔ ||||
|Francês (fr-FR)|✔||||
|Francês (fr-CA)|✔||||
|Alemão|||✔|✔|
|Italiano|✔||||
|Japonês||||✔|
|Coreano||✔||✔|
|Português (Brasil)|✔||||
|Espanhol (es-ES)|✔||||
|Espanhol (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Versões do criador de token personalizado

As culturas a seguir têm versões de criador de token personalizado:

|Cultura|Versão|Finalidade|
|--|--|--|
|Alemão<br>`de-de`|1.0.0|Cria tokens palavras dividindo-os usando um tokenizer baseada em aprendizado de máquina que tenta dividir palavras compostas em seus componentes únicos.<br>Se um usuário digita `Ich fahre einen krankenwagen` como uma expressão, ele será transformado em `Ich fahre einen kranken wagen`. Permitindo que a marcação da `kranken` e `wagen` independentemente como entidades diferentes.|
|Alemão<br>`de-de`|1.0.1|Cria tokens palavras dividindo-os em espaços.<br> Se um usuário inserir `Ich fahre einen krankenwagen` como uma expressão, ele permanecerá um único token. Portanto, `krankenwagen` está marcado como uma única entidade. |

### <a name="migrating-between-tokenizer-versions"></a>Migrando entre as versões do criador de token

Sua primeira opção é alterar a versão do criador de token no arquivo do aplicativo, em seguida, importe a versão. Esta ação altera como as declarações são indexadas, mas permite que você mantenha a mesma ID de aplicativo. 

Criador de token JSON para 1.0.0. Observe o valor da propriedade `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Criador de token JSON para a versão 1.0.1. Observe o valor da propriedade `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

A segunda opção é [importe o arquivo como um novo aplicativo](luis-how-to-start-new-app.md#import-an-app-from-file), em vez de uma versão. Essa ação significa que o novo aplicativo tem uma ID de aplicativo diferente, mas usa a versão do criador de token especificada no arquivo. 