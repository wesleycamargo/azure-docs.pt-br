---
title: Idiomas compatíveis – API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Uma lista dos idiomas naturais compatíveis com a API de Tradução de Texto.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: article
ms.date: 09/25/2018
ms.author: v-jansko
ms.openlocfilehash: d0ab683ac59c17cbae1e0947cd2acd785b0365d3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884963"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Idiomas e regiões compatíveis com a API de Tradução de Texto

A API de Tradução de Texto dá suporte aos seguintes idiomas para tradução de texto em texto. A Neural Machine Translation (NMT) é o novo padrão para traduções automáticas de alta qualidade alimentadas por AI e está disponível como padrão usando a V3 da API de texto do tradutor quando um sistema neural está disponível. 

[Saiba mais sobre como a tradução automática funciona](https://www.microsoft.com/translator/mt.aspx)

**API do Tradutor V2**

> [!NOTE]
> A V2 será preterida em 30 de abril de 2018 e será descontinuada em 30 de abril de 2019.

* Estatística apenas: nenhum sistema neural está disponível para esse idioma.
* Neural disponível: um sistema neural está disponível. Use o parâmetro `category=generalnn` para acessar o sistema neural.
* Neural padrão: neural é o sistema de tradução padrão. Use o parâmetro `category=smt` para acessar o sistema estatístico para uso com o Hub do Microsoft Translator.
* Neural apenas: apenas a tradução neural está disponível.

**API do Tradutor V3** a API do Tradutor V3 é neural por padrão e sistemas estatísticos estão disponíveis somente quando não houver nenhum sistema neural. O Tradutor Personalizado pode ser usado apenas com linguagens neurais. 

|Linguagem|  Código de idioma|  API V2| API V3|
|:-----|:-----:|:-----|:-----|
|Africâner| `af`    |Estatística apenas|  Neural|
|Árabe|    `ar`    |Neural disponível|  Neural|
|Bangla|    `bn`    |Neural disponível|  Neural|
|Bósnio (latim)|   `bs`    |Neural disponível|  Neural|
|Búlgaro| `bg`    |Neural disponível|  Neural|
|Cantonês (tradicional)|   `yue`   |Estatística apenas|  Estatística|
|Catalão|   `ca`    |Estatística apenas|  Estatística|
|Chinês (simplificado)|    `zh-Hans`   |Neural padrão |Neural|
|Chinês (tradicional)|   `zh-Hant`   |Neural padrão |Neural|
|Croata|  `hr`    |Neural disponível|  Neural|
|Tcheco| `cs`    |Neural disponível|  Neural|
|Dinamarquês|    `da`    |Neural disponível   |Neural|
|Holandês| `nl`    |Neural disponível|  Neural|
|Inglês|   `en`    |Neural disponível|  Neural|
|Estoniano|  `et`    |Neural disponível|  Neural|
|Fijiano|    `fj`    |Estatística apenas|  Estatística|
|Filipino|  `fil`   |Estatística apenas|  Estatística|
|Finlandês|   `fi`    |Neural disponível|  Neural|
|Francês|    `fr`    |Neural disponível|  Neural|
|Alemão|    `de`    |Neural disponível|  Neural|
|Grego| `el`    |Neural disponível|  Neural|
|Crioulo haitiano|    `ht`    |Estatística apenas   |Estatística|
|Hebraico |`he`   |Neural disponível   |Neural|
|Híndi| `hi`    |Neural padrão|    Neural|
|Hmong Daw| `mww`   |Estatística apenas|  Estatística|
|Húngaro| `hu`    |Neural disponível|  Neural|
|Islandês| `is`    |Neural apenas|   Neural|
|Indonésio|    `id`    |Estatística apenas|  Estatística|
|Italiano|   `it`    |Neural disponível|  Neural|
|Japonês|  `ja`    |Neural disponível|  Neural|
|Suaíli| `sw`    |Estatística apenas|  Estatística|
|Klingon|   `tlh`   |Estatística apenas|  Estatística|
|Klingon (plqaD)|   `tlh-Qaak`  |Estatística apenas|  Estatística|
|Coreano |`ko`   |Neural disponível|  Neural|
|Letão|   `lv`    |Neural disponível|  Neural|
|Lituano|    `lt`    |Neural disponível|  Neural|
|Malgaxe|  `mg`    |Estatística apenas|  Estatística|
|Malaio| `ms`    |Estatística apenas   |Estatística|
|Maltês|   `mt`    |Estatística apenas|  Estatística|
|Norueguês| `nb`    |Neural disponível|  Neural|
|Persa|   `fa`    |Estatística apenas|  Estatística|
|Polonês|    `pl`    |Neural disponível|  Neural|
|Português|    `pt`    |Neural disponível|  Neural|
|Queretaro Otomi|   `otq`   |Estatística apenas|  Estatística|
|Romeno|  `ro`    |Neural disponível|  Neural|
|Russo|   `ru`    |Neural disponível|  Neural|
|Samoano|    `sm`    |Estatística apenas|  Estatística|
|Sérvio (cirílico)|    `sr-Cyrl`   |Estatística apenas|  Estatística|
|Sérvio (latino)|   `sr-Latn`   |Estatística apenas   |Estatística|
|Eslovaco|    `sk`    |Neural disponível|  Neural|
|Esloveno| `sl`    |Neural disponível|  Neural|
|Espanhol|   `es`    |Neural disponível|  Neural|
|Sueco|   `sv`    |Neural disponível   |Neural|
|Taitiano|  `ty`    |Estatística apenas|  Estatística|
|Tâmil| `ta`    |Estatística apenas|  Estatística|
|Télugo|    `te`    |Neural apenas|   Neural|
|Tailandês|  `th`    |Neural disponível|  Neural|
|Tongan|    `to`    |Estatística apenas|  Estatística|
|Turco|   `tr`    |Neural disponível   |Neural|
|Ucraniano| `uk`    |Neural disponível|  Neural|
|Urdu|  `ur`    |Estatística apenas|  Estatística|
|Vietnamita|    `vi`    |Neural disponível|  Neural|
|Galês| `cy`    |Neural disponível|  Neural|
|Yucatec Maya|  `yua`   |Estatística apenas|  Estatística|

## <a name="transliteration"></a>Transliteração

O método Transliterate suporta os seguintes idiomas. Em "Para / De", "<->" indica que o idioma pode ser transliterado de ou para qualquer um dos scripts listados. O "->" indica que o idioma só pode ser transliterado de um script para outro.

| Linguagem    | Código de idioma | Script | Para/De | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Árabe | `ar` | Árabe `Arab` | <--> | Latim `Latn` |
|Bangla  | `bn` | Bengalês `Beng` | <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês (simplificado) `Hans`| <--> | Latim `Latn` |
| Chinês (Simplificado) | `zh-Hans` | Chinês simplificado `Hans`| <--> | Chinês tradicional `Hant`|
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Latim `Latn` |
| Chinês (Tradicional) | `zh-Hant` | Chinês tradicional `Hant`| <--> | Chinês simplificado `Hans` |
| Guzerate | `gu`  | Guzerate `Gujr` | --> | Latim `Latn` |
| Hebraico | `he` | Hebraico `Hebr` | <--> | Latim `Latn` |
| Híndi | `hi` | Devanagari `Deva` | <--> | Latim `Latn` |
| Japonês | `ja` | Japonês `Jpan` | <--> | Latim `Latn` |
| Canarim | `kn` | Canarim `Knda` | --> | Latim `Latn` |
| Malaiala | `ml` | Malaiala `Mlym` | --> | Latim `Latn` |
| Marati | `mr` | Devanagari `Deva` | --> | Latim `Latn` |
| Oriya | `or` | Oriya `Orya` | <--> | Latim `Latn` |
| Panjabi | `pa` | Gurmukhi `Guru`  | <--> | Latim `Latn`  |
| Sérvio (cirílico) | `sr-Cyrl` | Cirílico `Cyrl`  | --> | Latim `Latn` |
| Sérvio (latino) | `sr-Latn` | Latim `Latn` | --> | Cirílico `Cyrl`|
| Tâmil | `ta` | Tâmil `Taml` | --> | Latim `Latn` |
| Télugo | `te` | Télugo `Telu` | --> | Latim `Latn` |
| Tailandês | `th` | Tailandês `Thai` | <--> | Latim `Latn` |

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
| Suaíli      | `sw`          |
| Klingon      | `tlh`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malaio      | `ms`          |
| Maltês      | `mt`          |
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

## <a name="detect"></a>Detect

Os seguintes idiomas podem ser compatíveis pelo método Detectar. Detectar pode detectar idiomas que o Microsoft Translator não consegue traduzir.

| Linguagem    |
|:----------- |
| Africâner |
| Albanês |
| Árabe |
| Basco |
| Bielorrusso |
| Búlgaro |
| Catalão |
| Chinês |
| Chinês (Simplificado) |
| Chinês (Tradicional) |
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
| Crioulo haitiano |
| Hebraico |
| Híndi |
| Húngaro |
| Islandês |
| Indonésio |
| Irlandês |
| Italiano |
| Japonês |
| Coreano |
| Curdo (árabe) |
| Curdo (latino) |
| Latim |
| Letão |
| Lituano |
| Macedônio |
| Malaio |
| Maltês |
| Norueguês |
| Norueguês (Nynorsk) |
| Pashto |
| Persa |
| Polonês |
| Português |
| Romeno |
| Russo |
| Sérvio (cirílico) |
| Sérvio (latino) |
| Eslovaco |
| Esloveno |
| Somali |
| Espanhol |
| Swahili |
| Sueco |
| Tagalo |
| Télugo |
| Tailandês |
| Turco |
| Ucraniano |
| Urdu |
| Uzbeque (cirílico) |
| Uzbeque (latino) |
| Vietnamita |
| Galês |
| Iídiche |

## <a name="access-the-translator-text-api-language-list-programmatically"></a>Acessar a lista de idiomas da API de Tradução de Texto de forma programática

Você pode recuperar uma lista de idiomas com suporte para a API de Tradução de Texto v3.0 usando o método de Idiomas. Você pode visualizar a lista por recurso, código de idioma, bem como o nome do idioma em inglês ou qualquer outro idioma suportado. Essa lista é atualizada automaticamente pelo serviço Microsoft Translator conforme novos idiomas são disponibilizados.

[Exibir documentação de referência de operação de idiomas](reference/v3-0-languages.md)

## <a name="customization"></a>Personalização

Os seguintes idiomas estão disponíveis para a personalização usando o [Tradutor Personalizado](http://aka.ms/CustomTranslator).

| Linguagem    | Código de idioma |
|:----------- |:-------------:|
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Chinês (simplificado)      | `zh-Hans`          |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Inglês    | `en`     |
| Estoniano      | `et`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Hebraico      | `he`          |
| Híndi      | `hi`          |
| Húngaro      | `hu`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Norueguês      | `nb`          |
| Polonês      | `pl`          |
| Português      | `pt`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Tailandês      | `th`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Vietnamita      | `vi`          |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Acesse a lista no site do Microsoft Translator

Para uma rápida olhada nos idiomas, o site Microsoft Translator mostra todos os idiomas suportados pelas APIs Translator Text and Speech. Esta lista não inclui informações específicas do desenvolvedor, como códigos de idioma.

[Veja a lista de idiomas](https://www.microsoft.com/translator/languages.aspx)
