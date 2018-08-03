---
title: Suporte à localização usando aplicativos LUIS no Azure | Microsoft Docs
description: Saiba mais sobre os idiomas com suporte do LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2017
ms.author: diberry
ms.openlocfilehash: d2c479445aabe05013470724c623978402abeb9d
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248410"
---
# <a name="culture-specific-understanding-in-luis-apps"></a>Reconhecimento de especificidades da cultura em aplicativos LUIS

Um aplicativo LUIS é específico à cultura e não pode ser alterado após a definição. 

## <a name="multi-language-luis-apps"></a>Aplicativos LUIS com vários idiomas
Se você precisar de um aplicativo de cliente LUIS com vários idiomas, como um chatbot, terá algumas opções. Se o LUIS der suporte a todos os idiomas, desenvolva um aplicativo LUIS para cada idioma. Cada aplicativo LUIS tem uma ID de aplicativo exclusiva e um log de ponto de extremidade. Se você precisa fornecer o reconhecimento de idioma para um idioma ao qual o LUIS não oferece suporte, use a [API de Tradução da Microsoft](../Translator/translator-info-overview.md) para traduzir a declaração para um idioma com suporte, envie a declaração para o ponto de extremidade do LUIS e receba as pontuações resultantes.

## <a name="languages-supported"></a>Idiomas compatíveis
O LUIS compreende declarações nos seguintes idiomas:


| Linguagem |Local  |  Domínio predefinido | Entidade predefinida | Sugestões de frase | **[Análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) | 
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano |`en-US` | ✔ | ✔  |✔|✔|
| Francês do Canadá |`fr-CA` |-|   -   |-|✔|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandês |`nl-NL` |-|  -   |-|✔|
| Francês (França) |`fr-FR` |-| ✔ |✔ |✔|
| Alemão |`de-DE` |-| ✔ |✔ |✔|
| Italiano |`it-IT` |-| ✔ |✔|✔|
| *[Japonês](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Somente frase principal|
| Coreano |`ko-KR` |-|   -   |-|Somente frase principal|
| Português (Brasil) |`pt-BR` |-| ✔ |✔ |nem todas as subculturas|
| Espanhol (Espanha) |`es-ES` |-| ✔ |✔|✔|
| Espanhol (México)|`es-MX` |-|  -   |✔|✔|


O suporte aos idiomas varia para [entidades predefinidas](luis-reference-prebuilt-entities.md) e [domínios predefinidos](luis-reference-prebuilt-domains.md). 

### <a name="chinese-support-notes"></a>*Notas de suporte ao chinês

 - Na cultura `zh-cn`, o LUIS espera o conjunto de caracteres de chinês simplificado em vez do conjunto de caracteres tradicional.
 - Os nomes de intenções, entidades, recursos e expressões regulares podem estar em caracteres chineses ou romanos.
 - Veja a [referência a domínios predefinidos](luis-reference-prebuilt-domains.md) para obter informações sobre quais domínios predefinidos são compatíveis na cultura `zh-cn`.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>*Notas de suporte ao japonês

 - Como o LUIS não fornece análise sintática e não compreenderá a diferença entre Keigo e japonês informal, será necessário incorporar os diferentes níveis de formalidade como exemplos de treinamento para os seus aplicativos. 
     - でございます não é o mesmo que です. 
     - です não é o mesmo que だ. 

### <a name="text-analytics-support-notes"></a>**Notas de suporte a análises de texto
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

 