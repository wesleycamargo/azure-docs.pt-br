---
title: Suporte a idiomas – serviços de fala
titleSuffix: Azure Cognitive Services
description: Os Serviços de Fala do Azure dão suporte a vários idiomas para conversão de fala em texto e de texto em fala, bem com a tradução de fala. Este artigo fornece uma lista abrangente de suporte ao idioma por serviço.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9b8e12220f220bd8183675d13e25bdcab02707fd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020850"
---
# <a name="language-and-region-support-for-the-speech-services"></a>Suporte de idioma e região para os serviços de fala

Há suporte para diferentes idiomas para diferentes funções dos Serviços de Fala. As tabelas a seguir resumem o suporte ao idioma.

## <a name="speech-to-text"></a>Conversão de fala em texto

A API de reconhecimento de fala da Microsoft é compatível com os seguintes idiomas. Há diferentes níveis de personalização disponíveis para cada idioma.

  Código | Linguagem | [Adaptação acústica](how-to-customize-acoustic-models.md) | [Adaptação de idioma](how-to-customize-language-model.md) | [Adaptação de pronúncia](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | Árabe (Egito), padrão moderno | Não  | Sim | Não 
 ca-ES | Catalão | Não  | Não | Não 
 da-DK | Dinamarquês (Dinamarca) | Não  | Não | Não 
 de-DE | Alemão (Alemanha) | Sim | sim | Não 
 en-AU | Inglês (Austrália) | Não  | sim | Sim
 en-CA | Inglês (Canadá) | Não  | sim | Sim
 en-GB | Inglês (Reino Unido) | Não  | sim | Sim
 en-IN | Inglês (Índia) | Sim | sim | Sim
 en-NZ | Inglês (Nova Zelândia) | Não  | sim | Sim  
 en-US | Inglês (Estados Unidos) | Sim | sim | Sim
 es-ES | Espanhol (Espanha) | Sim | sim | Não 
 es-MX | Espanhol (México) | Não  | Sim | Não 
 fi-FI | Finlandês (Finlândia) | Não  | Não | Não 
 fr-CA | Francês (Canadá) | Não  | Sim | Não 
 fr-FR | Francês (França) | Sim | sim | Não 
 hi-IN | Hindi (Índia) | Não  | Sim | Não 
 it-IT | Italiano (Itália) | Sim | sim | Não 
 ja-JP | Japonês (Japão) | Não  | Sim | Não 
 ko-KR | Coreano (Coreia do Sul) | Não  | Sim | Não 
 nb-NO | Norueguês (Bokmål) (Noruega) | Não  | Não | Não 
 nl-NL | Holandês (Países Baixos) | Não  | Sim | Não 
 pl-PL | Polonês (Polônia) | Não  | Não | Não 
 pt-BR | Português (Brasil) | Sim | sim | Não 
 pt-PT | Português (Portugal) | Não  | Sim | Não 
 ru-RU | Russo (Rússia) | Sim | sim | Não 
 sv-SE | Sueco (Suécia) | Não  | Não | Não 
 zh-CN | Chinês (mandarim, simplificado) | Sim | sim | Não 
 zh-HK | Chinês (Cantonês, tradicional) | Não  | Sim | Não 
 zh-TW | Chinês (Mandarim Taiwanês) | Não  | Sim | Não 
 th-TH | Tailandês (Tailândia) | Não  | Não | Não 


## <a name="text-to-speech"></a>Conversão de texto em fala

A API REST de conversão de texto em fala dá suporte às seguintes vozes, cada uma delas compatível com um idioma e dialeto específicos, identificados pela localidade.

> [!IMPORTANT]
> Os preços variam para vozes padrão, personalizadas e neurais. Visite a página [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) para obter mais informações.

### <a name="neural-voices"></a>Vozes neurais

A conversão de texto em fala neural é um novo tipo de sintetização de fala alimentado por redes neurais profundas. Ao usar uma voz neural, a fala sintetizada é quase indistinguível de gravações de humana.

Vozes neurais podem ser usadas para interagir com chatbots e assistentes virtuais de forma mais natural e participativa para converter textos digitais, como livros eletrônicos, em audiolivros, e aprimorar sistemas de navegação de carros. Com a prosódia natural semelhante à humana e a articulação clara das palavras, as vozes neurais reduzem significativamente a fadiga de escuta quando os usuários interagem com sistemas de inteligência artificial.

Para obter uma lista completa de vozes neurais e a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Local | Linguagem | Gênero | Mapeamento de nome de serviço completo | Nome curto de voz
--------|----------|--------|---------|------------
de-DE | Alemão (Alemanha) | Feminino | "Microsoft Server fala texto para fala voz (de-DE, KatjaNeural)" | "de-DE-KatjaNeural"
en-US | Inglês (EUA) | Masculino | "Conversão de Texto em Fala do Microsoft Server (en-US, GuyNeural)" | "en-US-GuyNeural"
en-US | Inglês (EUA) | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-US, JessaNeural)" | "en-US-JessaNeural"
it-IT | Italiano (Itália) | Feminino |"Microsoft Server fala texto para fala voz (it-IT, ElsaNeural)" | "it-IT-ElsaNeural"
zh-CN | Chinês (continente) | Feminino | "Voz para Conversão de Texto em Fala de Fala do Microsoft Server (zh-CN, XiaoxiaoNeural)" | "zh-CN-XiaoxiaoNeural"

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome curto de voz em suas solicitações de síntese de fala.

### <a name="standard-voices"></a>Vozes padrão

Mais de 75 vozes padrão estão disponíveis em mais de 45 idiomas e localidades, que permitem converter texto em fala sintetizada. Para obter mais informações sobre a disponibilidade regional, consulte [regiões](regions.md#standard-and-neural-voices).

Local | Linguagem | Gênero | Mapeamento de nome de serviço completo | Nome curto de voz
-------|----------|---------|----------|----------
ar-EG\* | Árabe (Egito) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-EG, Hoda)” | "ar-EG-Hoda"
ar-SA | Árabe (Arábia Saudita) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-SA, Naayf)” | "ar-SA-Naayf"
bg-BG | Búlgaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (bg-BG, Ivan)” | "bg-BG-Ivan"
ca-ES | Catalão (Espanha) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ca-ES, HerenaRUS)” | "ca-ES-HerenaRUS"
cs-CZ | Tcheco | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (cs-CZ, Jakub)” | "cs-CZ-Jakub"
da-DK | Dinamarquês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (da-DK, HelleRUS)” | "da-DK-HelleRUS"
de-AT | Alemão (Áustria) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-AT, Michael)” | "de-AT-Michael"
de-CH | Alemão (Suíça) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-CH, Karsten)” | "de-CH-Karsten"
de-DE | Alemão (Alemanha) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Hedda)" | "de-DE-Hedda"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, HeddaRUS)” | "de-DE-HeddaRUS"
| | | Masculino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Stefan, Apollo)" | "de-DE-Stefan-Apollo"
el-GR | Grego | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (el-GR, Stefanos)” | "el-GR-Stefanos"
en-AU | Inglês (Austrália) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, Catherine)" | "en-AU-Catherine"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, HayleyRUS)” | "en-AU-HayleyRUS"
en-CA | Inglês (Canadá) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, Linda)” | "en-CA-Linda"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, HeatherRUS)” | "en-CA-HeatherRUS"
en-GB | Inglês (Reino Unido) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, Susan, Apollo)” | "en-GB-Susan-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, HazelRUS)” | "en-GB-HazelRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, George, Apollo)” | "en-GB-George-Apollo"
en-IE | Inglês (Irlanda) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IE, Sean)” | "en-IE-Sean"
en-IN | Inglês (Índia) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Heera, Apollo)” | "en-IN-Heera-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, PriyaRUS)” | "en-IN-PriyaRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Ravi, Apollo)” | "en-IN-Ravi-Apollo"
en-US | Inglês (EUA) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, ZiraRUS)” | "en-US-ZiraRUS"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, JessaRUS)” | "en-US-JessaRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, BenjaminRUS)” | "en-US-BenjaminRUS"
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-US, Jessa24kRUS)" | "en-US-Jessa24kRUS"
| | | Masculino | "Conversão de Texto em Fala do Microsoft Server (en-US, Guy24kRUS)" | "en-US-Guy24kRUS"
es-ES | Espanhol (Espanha) |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Laura, Apollo)” | "es-ES-Laura-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, HelenaRUS)” | "es-ES-HelenaRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Pablo, Apollo)” | "es-ES-Pablo-Apollo"
es-MX | Espanhol (México) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, HildaRUS)” | "es-MX-HildaRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, Raul, Apollo)” | "es-MX-Ricardo-Apollo"
fi-FI | Finlandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fi-FI, HeidiRUS)” | "fi-FI-HeidiRUS"
fr-CA | Francês (Canadá) |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, Caroline)” | "fr-CA-Caroline"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, HarmonieRUS)” | "fr-CA-HarmonieRUS"
fr-CH | Francês (Suíça)| Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CH, Guillaume)” | "fr-CH-Guillaume"
fr-FR | Francês (França)| Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Julie, Apollo)” | "fr-FR-Julie-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, HortenseRUS)” | "fr-FR-HortenseRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Paul, Apollo)” | "fr-FR-Paul-Apollo"
he-IL| Hebraico (Israel) | Masculino| “Voz da Conversão de Texto em Fala do Microsoft Server Speech (he-IL, Asaf)” | "he-IL-Asaf"
hi-IN | Hindi (Índia) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana, Apollo)” | "hi-IN-Kalpana-Apollo"
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana)” | "hi-IN-Kalpana"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Hemant)” | "hi-IN-Hemant"
hr-HR | Croata | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hr-HR, Matej)” | "hr-HR-Matej"
hu-HU | Húngaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hu-HU, Szabolcs)” | "hu-HU-Szabolcs"
id-ID | Indonésio| Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (id-ID, Andika)” | "id-ID-Andika"
it-IT | Italiano | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (it-IT, Cosimo, Apollo)” | "it-IT-Cosimo-Apollo"
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (it-IT, LuciaRUS)" | "it-IT-LuciaRUS"
ja-JP | Japonês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ayumi, Apollo)” | "ja-JP-Ayumi-Apollo"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ichiro, Apollo)” | "ja-JP-Ichiro-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, HarukaRUS)” | "ja-JP-HarukaRUS"
ko-KR | Coreano | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ko-KR, HeamiRUS)” | "ko-KR-HeamiRUS"
ms-MY | Malaio | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ms-MY, Rizwan)” | "ms-MY-Rizwan"
nb-NO | Norueguês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nb-NO, HuldaRUS)” | "nb-NO-HuldaRUS"
nl-NL | Holandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nl-NL, HannaRUS)” | "nl-NL-HannaRUS"
pl-PL | Polonês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pl-PL, PaulinaRUS)” | "pl-PL-PaulinaRUS"
pt-BR | Português (Brasil) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, HeloisaRUS)” | "pt-BR-HeloisaRUS"
| | | Masculino |“Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, Daniel, Apollo)” | "pt-BR-Daniel-Apollo"
pt-PT | Português (Portugal) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-PT, HeliaRUS)” | "pt-PT-HeliaRUS"
ro-RO | Romeno | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ro-RO, Andrei)” | "ro-RO-Andrei"
ru-RU |Russo| Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Irina, Apollo)” | "ru-RU-Irina-Apollo"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Pavel, Apollo)” | "ru-RU-Pavel-Apollo"
| | | Feminino | "Conversão de Texto em Fala do Microsoft Server (ru-RU, EkaterinaRUS)" | ru-RU-EkaterinaRUS
sk-SK | Eslovaco | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sk-SK, Filip)” | "sk-SK-Filip"
sl-SI | Esloveno | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sl-SI, Lado)” | "sl-SI-Lado"
sv-SE | Sueco | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sv-SE, HedvigRUS)” | "sv-SE-HedvigRUS"
ta-IN | Tâmil (Índia) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ta-IN, Valluvar)” | "ta-IN-Valluvar"
te-IN | Télugo (Índia) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (te-IN, Chitra)" | "te-IN-Chitra"
th-TH | Tailandês | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (th-TH, Pattara)” | "th-TH-Pattara"
tr-TR | Turco | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (tr-TR, SedaRUS)” | "tr-TR-SedaRUS"
vi-VN | Vietnamita | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (vi-VN, An)” | "vi-VN-An"
zh-CN | Chinês (continente) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, HuihuiRUS)” | "zh-CN-HuihuiRUS"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Yaoyao, Apollo)” | "zh-CN-Yaoyao-Apollo"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Kangkang, Apollo)” | "zh-CN-Kangkang-Apollo"
zh-HK | Chinês (Hong Kong) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Tracy, Apollo)” | "zh-HK-Tracy-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, TracyRUS)” | "zh-HK-TracyRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Danny, Apollo)” | "zh-HK-Daniel-Apollo"
zh-TW | Chinês (Taiwan) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Yating, Apollo)” | "zh-TW-Yating-Apollo"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, HanHanRUS)” | "zh-TW-HanHanRUS"
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Zhiwei, Apollo)” | "zh-TW-Zhiwei-Apollo"

\* *ar-EG é compatível com MSA (árabe padrão moderno).*

> [!NOTE]
> Você pode usar o mapeamento de nome de serviço completo ou o nome curto de voz em suas solicitações de síntese de fala.

### <a name="customization"></a>Personalização

Personalização de voz está disponível para de-DE, en-GB, en-IN, en-US, es-MX, fr-FR, it-IT, pt-BR e zh-CN. Selecione a localidade do direito que corresponde aos dados de treinamento que você tem para treinar um modelo de voz personalizadas. Por exemplo, se a gravação de dados que você tem são falados em inglês com uma ênfase Britânica, selecione en-GB.  

> [!NOTE]
> Não oferecemos suporte treinamento do modelo bilíngues em voz personalizados, exceto o bilíngues inglês chinês. Selecione 'Bilíngue chinês inglês' se você quiser treinar uma voz chinês que pode falam inglês também. Treinamento de voz em todas as localidades começa com um conjunto de dados de mais de 2.000 declarações, exceto para o en-US e zh-CN, onde você pode iniciar com qualquer tamanho de dados de treinamento.

## <a name="speech-translation"></a>Tradução de fala

A API de **Tradução de Fala** é compatível com diferentes idiomas para conversão de fala em fala e de fala em texto. O idioma de origem sempre deve ser da tabela de idioma conversão de fala em texto. Os idiomas de destino disponíveis dependem de se o destino de tradução é fala ou texto. Você pode traduzir a entrada de fala em mais de [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto desses idiomas está disponível para [síntese de fala](language-support.md#text-languages).

### <a name="text-languages"></a>Idiomas de texto

| Idioma de texto    | Código de idioma |
|:----------- |:-------------:|
| Africâner      | `af`          |
| Árabe       | `ar`          |
| Bangla      | `bn`          |
| Bósnio (latim)      | `bs`          |
| Búlgaro      | `bg`          |
| Cantonês (tradicional)      | `yue`          |
| Catalão      | `ca`          |
| Chinês (simplificado)      | `zh-Hans`          |
| Chinês (tradicional)      | `zh-Hant`          |
| Croata      | `hr`          |
| Tcheco      | `cs`          |
| Dinamarquês      | `da`          |
| Holandês      | `nl`          |
| Inglês      | `en`          |
| Estoniano      | `et`          |
| Fijiano      | `fj`          |
| Filipino      | `fil`          |
| Finlandês      | `fi`          |
| Francês      | `fr`          |
| Alemão      | `de`          |
| Grego      | `el`          |
| Crioulo haitiano      | `ht`          |
| Hebraico      | `he`          |
| Híndi      | `hi`          |
| Hmong Daw      | `mww`          |
| Húngaro      | `hu`          |
| Indonésio      | `id`          |
| Italiano      | `it`          |
| Japonês      | `ja`          |
| Suaíli      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Coreano      | `ko`          |
| Letão      | `lv`          |
| Lituano      | `lt`          |
| Malgaxe      | `mg`          |
| Malaio      | `ms`          |
| Maltês      | `mt`          |
| Norueguês      | `nb`          |
| Persa      | `fa`          |
| Polonês      | `pl`          |
| Português      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Romeno      | `ro`          |
| Russo      | `ru`          |
| Samoano      | `sm`          |
| Sérvio (cirílico)      | `sr-Cyrl`          |
| Sérvio (latino)      | `sr-Latn`          |
| Eslovaco     | `sk`          |
| Esloveno      | `sl`          |
| Espanhol      | `es`          |
| Sueco      | `sv`          |
| Taitiano      | `ty`          |
| Tâmil      | `ta`          |
| Tailandês      | `th`          |
| Tongan      | `to`          |
| Turco      | `tr`          |
| Ucraniano      | `uk`          |
| Urdu      | `ur`          |
| Vietnamita      | `vi`          |
| Galês      | `cy`          |
| Yucatec Maya      | `yua`          |


## <a name="next-steps"></a>Próximas etapas

* [Obter sua assinatura de avaliação de Serviços de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
