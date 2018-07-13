---
title: API de Tradução de Texto da Microsoft – Migrar para V3 | Microsoft Docs
description: Saiba como migrar da V2 para a V3 da API de Tradução de Texto.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: translator
ms.topic: article
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: 16fec351af5b5b3875657ee244c18f305311d965
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364552"
---
# <a name="microsoft-translator-text-api-v2-to-v3-migration"></a>API de Tradução de Texto da Microsoft, migração da V2 para V3

A equipe do Microsoft Translator lançou a V3 (Versão 3) da API de Tradução de Texto. Esta versão inclui novos recursos, métodos preteridos e um novo formato para enviar e receber dados do Serviço do Microsoft Translator. Este documento apresenta informações sobre como alterar os aplicativos para usar a V3. A V2 será preterida em 30 de abril de 2018 e será descontinuada em 30 de abril de 2019.

O fim deste documento contém links úteis para saber mais.

## <a name="summary-of-features"></a>Resumo dos recursos

* Sem rastreamento – na V3, Sem Rastreamento se aplica a todas as camadas de preços no portal do Azure. Isso significa que nenhum texto enviado para a API V3 será salvo pela Microsoft.
* JSON – XML é substituído por JSON. Todos os dados enviados para o serviço e recebidos do serviço estão no formato JSON.
* Vários idiomas de destino em uma única solicitação – o método Translate aceita vários idiomas de destino para tradução em uma única solicitação. Por exemplo, uma única solicitação pode ser 'de' inglês e 'para' alemão, espanhol e japonês ou qualquer outro grupo de idiomas.
* Dicionário bilíngue – um método de dicionário bilíngue foi adicionado à API. Esse método inclui 'pesquisa' e 'exemplos'.
* Transliterate – um método transliterate foi adicionado à API. Esse método converterá palavras e frases em um script (ex.: árabe) em outro script (ex.: latim).
* Idiomas – um novo método de 'idiomas' fornece informações de idioma, no formato JSON, para uso com os métodos 'translate', 'dictionary' e 'transliterate'.
* Não familiarizado com a Tradução – novas funcionalidades foram adicionadas ao método 'translate' para dar suporte a alguns dos recursos que estavam na API V2 como métodos separados. Um exemplo é TranslateArray.
* Método Speak – a funcionalidade de conversão de texto em fala não é mais compatível com a API do Microsoft Translator. A funcionalidade de conversão de texto em fala está disponível na API de Fala do Bing dos Serviços Cognitivos do Microsoft Azure.

A seguinte lista de métodos V2 e V3 identifica as APIs e os métodos da V3 que fornecerão a funcionalidade disponibilizada com a V2.

| Método da API V2   | Compatibilidade da API V3 |
|:----------- |:-------------|
| Translate     | Translate          |
| TranslateArray      | Translate          |
| GetLanguageNames      | Linguagens          |
| GetLanguagesForTranslate     | Linguagens        |
| GetLanguagesForSpeak      | Speech API de Serviços Cognitivos         |
| Speak     | Speech API de Serviços Cognitivos          |
| Detectar     | Detectar         |
| DetectArray     | Detectar         |
| AddTranslation     | API do Hub do Microsoft Translator         |
| AddTranslationArray    | API do Hub do Microsoft Translator          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | Não há mais suporte para o recurso         |
| GetTranslationsArray      | Não há mais suporte para o recurso         |

## <a name="move-to-json-format"></a>Mover para o formato JSON

A Tradução de Texto do Microsoft Translator V2 aceitava e retornava dados em formato XML. Na V3, todos os dados enviados e recebidos usando a API está em formato JSON. XML não será mais aceito nem retornado na V3. 

Esta alteração afetará vários aspectos de um aplicativo escrito para a API de Tradução de Texto V2. Por exemplo, a API de Idiomas retorna informações de idioma para os métodos de tradução de texto, transliteração e os dois de dicionário. Você pode solicitar todas as informações de idioma para todos os métodos em uma chamada ou solicitá-los individualmente.

O método languages não exige autenticação; ao clicar no link a seguir, você pode ver todas as informações de idioma para V3 em JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Chave de autenticação

A chave de autenticação que você está usando para a V2 será aceita para V3. Você não precisará obter uma nova assinatura. Você poderá combinar V2 e V3 em seus aplicativos durante o período de migração de um ano, tornando mais fácil para você liberar novas versões enquanto ainda está migrando de V2-XML para V3-JSON.

## <a name="pricing-model"></a>Modelo de preços

O preço do Microsoft Translator V3 é definido da mesma maneira que o da V2; por caractere, incluindo espaços. Os novos recursos na V3 fazem algumas alterações em que caracteres contam para cobrança.

| Método da V3   | Caracteres contados para cobrança |
|:----------- |:-------------|
| Linguagens     | Nenhum caractere enviado, nenhum contado, nenhum encargo.          |
| Translate     | A contagem é baseada em quantos caracteres são enviado para tradução e para quantos idiomas os caracteres são traduzidos. Cinquenta caracteres enviados e cinco idiomas solicitados serão 50 x 5.           |
| Transliterate     | O número de caracteres enviados para transliteração é contado.         |
| Dictionary lookup & example     | O número de caracteres enviados para pesquisa no Dicionário e exemplos é contato.         |
| BreakSentence     | Sem Encargos.       |
| Detectar     | Sem Encargos.      |

## <a name="v3-end-points"></a>Pontos de extremidade da V3

Global

* api.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>Métodos de traduções de texto da API V3

[Idiomas](reference/v3-0-languages.md)

[Translate](reference/v3-0-translate.md)

[Transliterate](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[Detect](reference/v3-0-detect.md)

[Dictionary/lookup](reference/v3-0-dictionary-lookup.md)

[Dictionary/example](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>Personalização

Por padrão, o Microsoft Translator V3 usa tradução automática neural. Assim, ele não pode ser usado com o Hub do Microsoft Translator, que é compatível somente com tradução automática estatística herdada. A personalização para tradução neural agora está disponível usando o Tradutor Personalizado. [Saiba mais sobre como personalizar a tradução automática neural](customization.md)

Tradução neural com a API de texto V3 não é compatível com o uso de categorias padrão (smt, fala, texto, generalnn).


## <a name="links"></a>Links

* [Política de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informações legais do Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Termos de serviços online](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exibir a documentação da V3.0](reference/v3-0-reference.md)
