---
title: Idiomas suportados na API do Microsoft Translator | Microsoft Docs
description: Exibir idiomas suportados pela API de texto do Microsoft Translator.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: v-jansko
ms.openlocfilehash: 5542c1c0eee5e4c7c85c566908dc8999b118f06e
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37032428"
---
# <a name="supported-languages-in-the-microsoft-translator-text-api"></a>Idiomas suportados na API de texto do Microsoft Translator

A API de texto do Microsoft Translator suporta os seguintes idiomas para conversão de texto em texto. A Neural Machine Translation (NMT) é o novo padrão para traduções automáticas de alta qualidade alimentadas por AI e está disponível como padrão usando a V3 da API de texto do tradutor quando um sistema neural está disponível. A tradução automática neural está disponível em V2 usando a categoria "generalnn".

[Saiba mais sobre como a tradução automática funciona](https://www.microsoft.com/translator/mt.aspx)

| Linguagem    | Tipo de tradução |Código de idioma |
|:----------- |:-------:|:-------------:|
| Africâner      | Estatística |`af`          |
| Árabe      | Neural | `ar`          |
| Árabe, levantino    | Neural | `apc`
| Bangla      | Neural |`bn`          |
| Bósnio (latim)      | Estatística |`bs`          |
| Búlgaro     |  Neural |`bg`          |
| Cantonês (tradicional)      | Estatística |`yue`          |
| Catalão      | Estatística |`ca`          |
| Chinês (simplificado)        |  Neural |`zh-Hans`          | 
| Chinês (tradicional)        |  Neural |`zh-Hant`          |
| Croata      | Neural |`hr`          |
| Tcheco        |  Neural |`cs`          |
| Dinamarquês        |  Neural |`da`          |
| Holandês        |  Neural |`nl`          |
| Inglês       |  Neural |`en`          |
| Estoniano      | Neural |`et`          |
| Fijiano      | Estatística |`fj`          |
| Filipino      | Estatística |`fil`          |
| Finlandês      | Neural |`fi`          |
| Francês        |  Neural |`fr`          |
| Alemão       |  Neural |`de`          |
| Grego      | Neural |`el`          |
| Crioulo haitiano      | Estatística |`ht`          |
| Hebraico      | Neural |`he`          |
| Híndi        |  Neural |`hi`          |
| Hmong Daw      | Estatística |`mww`          |
| Húngaro      | Neural |`hu`          |
| Islandês      |  Neural |`is`           |
| Indonésio      | Estatística |`id`          |
| Italiano        |  Neural |`it`          |
| Japonês        |  Neural |`ja`          |
| Kiswahili      | Estatística |`sw`          |
| Klingon      | Estatística |`tlh`          |
| Klingon (plqaD)      | Estatística |`tlh-Qaak`          |
| Coreano        |  Neural |`ko`          |
| Letão      | Neural |`lv`          |
| Lituano      | Neural |`lt`          |
| Malagasy      | Estatística |`mg`          |
| Malaio      | Estatística |`ms`          |
| Maltese      | Estatística |`mt`          |
| Norueguês        |  Neural |`nb`          |
| Persa      | Estatística |`fa`          |
| Polonês        |  Neural |`pl`          |
| Português        |  Neural |`pt`          |
| Queretaro Otomi      | Estatística |`otq`          |
| Romeno        |  Neural |`ro`          |
| Russo        |  Neural |`ru`          |
| Samoano      | Estatística |`sm`          |
| Sérvio (cirílico)      | Estatística |`sr-Cyrl`          |
| Sérvio (latino)      | Estatística |`sr-Latn`          |
| Eslovaco     | Neural |`sk`          |
| Esloveno      | Neural |`sl`          |
| Espanhol        |  Neural |`es`          |
| Sueco        |  Neural |`sv`          |
| Taitiano      | Estatística |`ty`          |
| Tâmil      | Estatística |`ta`          |
| Tailandês      | Neural |`th`          |
| Tongan      | Estatística |`to`          |
| Turco       |  Neural |`tr`          |
| Ucraniano      | Neural |`uk`          |
| Urdu      | Estatística |`ur`          |
| Vietnamita      | Neural |`vi`          |
| Galês      | Neural |`cy`          |
| Yucatec Maya      | Estatística |`yua`          |

## <a name="transliteration"></a>Transliteração

O método Transliterate suporta os seguintes idiomas. Em "Para / De", "<->" indica que o idioma pode ser transliterado de ou para qualquer um dos scripts listados. O "->" indica que o idioma só pode ser transliterado de um script para outro.

| Linguagem    | Código de idioma | Script | Para/De | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | ar | Árabe | <--> | Latim |
|Bangla  | bn | Bengalês | <--> | Latim |
| Chinês (Simplificado) | zh-Hans | Chinês (simplificado) | <--> | Latim |
| Chinês (Simplificado) | zh-Hans | Chinês (simplificado) | <--> | Chinês (tradicional) |
| Chinês (Tradicional) | zh-Hans | Chinês (tradicional) | <--> | Latim |
| Chinês (Tradicional) | zh-Hans | Chinês (tradicional) | <--> | Chinês (simplificado) |
| Guzerate | gu  | Guzerate | --> | Latim |
| Hebraico | he | Hebraico | <--> | Latim |
| Híndi | hi | Devanagari | <--> | Latim |
| Japonês | ja | Japonês | <--> | Latim |
| Canarim | kn | Canarim | --> | Latim |
| Malasiano | ml | Malaiala | --> | Latim |
| Marati | mr | Devanagari | --> | Latim |
| Oriya | ou o | Oriya | <--> | Latim |
| Panjabi | pa | Gurmukhi | <--> | Latim  |
| Sérvio (cirílico) | sr-Cyrl | Cirílico  | --> | Latim |
| Sérvio (latino) | sr-Latn | Latim | --> | Cirílico |
| Tâmil | ta | Tâmil | --> | Latim |
| Télugo | te | Télugo | --> | Latim |
| Tailandês | th | Tailandês | <--> | Latim |

## <a name="dictionary"></a>Dicionário

O dicionário suporta os seguintes idiomas para ou do inglês usando os métodos Lookup e Examples.

| Linguagem    | Código de idioma |
|:----------- |:-------------:|
| Africâner      | `af`          |
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Catalão      | `ca`          |
| Chinês (simplificado)      | `zh-Hans`          | 
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Estoniano      | `et`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Crioulo haitiano      | `ht`          |
| Hebraico      | `he`          |
| Híndi      | `hi`          |
| Hmong Daw      | `mww`          |
| Húngaro      | `hu`          |
| Islandês    | `is`  |
| Indonésio      | `id`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malaio      | `ms`          |
| Maltese      | `mt`          |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polonês      | `pl`          |
| Português      | `pt`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tâmil      | `ta`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |

## <a name="languages-detected-by-the-detect-method"></a>Idiomas detectados pelo método Detect

Os seguintes idiomas podem ser detectados pelo método Detectar. Detectar pode detectar idiomas que o Microsoft Translator não consegue traduzir.

| Linguagem    |
|:----------- |
| Africâner |
| Albanês |
| Árabe |
| Basco |
| Bielorrusso |
| Búlgaro |
| Catalão |
| Chinesa |
| Chinês_Simplificado |
| Chinês_Traditional |
| Croata |
| Tcheco |
| Dinamarquês |
| Holandês |
| Inglês |
| Esperanto |
| Estoniano |
| Finlandês |
| Francês |
| Galego |
| Alemão |
| Grego |
| Crioulo_haitiano |
| Hebraico |
| Híndi |
| Húngaro |
| Islandês |
| Indonésio |
| Irlandês |
| Italiano |
| Japonês |
| Coreano |
| Curdo_rabico |
| Curdo_latim |
| Latim |
| Letão |
| Lituano |
| Macedônio |
| Malaio |
| Maltese |
| Norueguês |
| Nynorsk_norueguês |
| Pashto |
| Persa |
| Polonês |
| Português |
| Romeno |
| Russo |
| Cirílico sérvio |
| sérvio Latino |
| Eslovaco |
| Esloveno |
| Somali |
| Espanhol |
| Swahili |
| Sueco |
| Tagalo |
| Tailandês |
| Turco |
| Ucraniano |
| Urdu |
| Uzbeque cirílico |
| Latim uzbeque |
| Vietnamita |
| Galês |
| Yiddish |

## <a name="access-the-list-programmatically"></a>Acessar a lista programaticamente

Você pode acessar a lista de idiomas suportados programaticamente usando a operação de idiomas da API de texto da V3.0. Você pode visualizar a lista por recurso, código de idioma, bem como o nome do idioma em inglês ou qualquer outro idioma suportado. Essa lista é atualizada automaticamente pelo serviço Microsoft Translator conforme novos idiomas são disponibilizados.

[Exibir documentação de referência de operação de idiomas](reference/v3-0-languages.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Acesse a lista no site do Microsoft Translator

Para uma rápida olhada nos idiomas, o site Microsoft Translator mostra todos os idiomas suportados pelas APIs Translator Text and Speech. Esta lista não inclui informações específicas do desenvolvedor, como códigos de idioma.

[Veja a lista de idiomas](https://www.microsoft.com/translator/languages.aspx)
