---
title: Suporte a idiomas – API de Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas com suporte do Serviço de Fala.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: erhopf
ms.openlocfilehash: fa4563b84eb0882832a89ccc98396ff487f51b48
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284754"
---
# <a name="language-and-region-support-for-speech-service-api"></a>Suporte ao idioma e à região para a API do Serviço de Fala

Diferentes idiomas têm suporte para diferentes funções do Serviço de Fala. As tabelas a seguir resumem o suporte ao idioma.

## <a name="speech-to-text"></a>Conversão de Fala em Texto

A API de reconhecimento de fala da Microsoft é compatível com os seguintes idiomas. Há diferentes níveis de personalização disponíveis para cada idioma.

  Código | Linguagem | [Adaptação acústica](how-to-customize-acoustic-models.md) | [Adaptação de idioma](how-to-customize-language-model.md) | [Adaptação de pronúncia](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | Árabe (Egito), padrão moderno | Não  | Sim | Não 
 ca-ES | Catalão (Espanha) | Não  | Não | Não 
 da-DK | Dinamarquês (Dinamarca) | Não  | Não | Não 
 de-DE | Alemão (Alemanha) | SIM | sim | Não 
 en-AU | Inglês (Austrália) | Não  | sim | SIM
 en-CA | Inglês (Canadá) | Não  | sim | SIM
 en-GB | Inglês (Reino Unido) | Não  | sim | SIM
 en-IN | Inglês (Índia) | SIM | sim | SIM
 en-NZ | Inglês (Nova Zelândia) | Não  | sim | SIM  
 en-US | Inglês (Estados Unidos) | SIM | sim | SIM
 es-ES | Espanhol (Espanha) | Não  | Sim | Não 
 es-MX | Espanhol (México) | Não  | Sim | Não 
 fi-FI | Finlandês (Finlândia) | Não  | Não | Não 
 fr-CA | Francês (Canadá) | Não  | Sim | Não 
 fr-FR | Francês (França) | SIM | sim | Não 
 hi-IN | Hindi (Índia) | Não  | Sim | Não 
 it-IT | Italiano (Itália) | SIM | sim | Não 
 ja-JP | Japonês (Japão) | Não  | Sim | Não 
 ko-KR | Coreano (Coreia do Sul) | Não  | Sim | Não 
 nb-NO | Norueguês (Bokmål) (Noruega) | Não  | Não | Não 
 nl-NL | Holandês (Países Baixos) | Não  | Sim | Não 
 pl-PL | Polonês (Polônia) | Não  | Não | Não 
 pt-BR | Português (Brasil) | Não  | Sim | Não 
 pt-PT | Português (Portugal) | Não  | Sim | Não 
 ru-RU | Russo (Rússia) | SIM | sim | Não 
 sv-SE | Sueco (Suécia) | Não  | Não | Não 
 zh-CN | Chinês (mandarim, simplificado) | SIM | sim | Não 
 zh-HK | Chinês (Mandarim, tradicional) | Não  | Sim | Não 
 zh-TW | Chinês (Mandarim Taiwanês) | Não  | Sim | Não 
 th-TH | Tailandês (Tailândia) | Não  | Não | Não 


## <a name="text-to-speech"></a>Texto em fala

A API de sintetização de voz oferece as seguintes vozes, cada uma das quais dá suporte a um idioma e um dialeto específicos, identificados por localidade.

Local | Linguagem | Gênero | Mapeamento do nome do serviço
-------|----------|---------|--------------------
ar-EG\* | Árabe (Egito) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-EG, Hoda)”
ar-SA | Árabe (Arábia Saudita) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ar-SA, Naayf)”
bg-BG | Búlgaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (bg-BG, Ivan)”
ca-ES | Catalão (Espanha) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ca-ES, HerenaRUS)”
cs-CZ | Tcheco | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (cs-CZ, Jakub)”
da-DK | Dinamarquês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (da-DK, HelleRUS)”
de-AT | Alemão (Áustria) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-AT, Michael)”
de-CH | Alemão (Suíça) | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-CH, Karsten)”
de-DE | Alemão (Alemanha) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Hedda)"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, HeddaRUS)”
| | | Masculino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (de-DE, Stefan, Apollo)"
el-GR | Grego | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (el-GR, Stefanos)”
en-AU | Inglês (Austrália) | Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, Catherine)"
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-AU, HayleyRUS)”
en-CA | Inglês (Canadá) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, Linda)”
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-CA, HeatherRUS)”
en-GB | Inglês (Reino Unido) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, Susan, Apollo)”
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, HazelRUS)”
| | |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-GB, George, Apollo)”
en-IE | Inglês (Irlanda) |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IE, Sean)”
en-IN | Inglês (Índia) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Heera, Apollo)”
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, PriyaRUS)”
| | |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-IN, Ravi, Apollo)”
en-US | Inglês (EUA) |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, ZiraRUS)”
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, JessaRUS)”
| | |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (en-US, BenjaminRUS)”
| | |Feminino | "Conversão de Texto em Fala do Microsoft Server (en-US, Jessa24kRUS)"
| | |Masculino | "Conversão de Texto em Fala do Microsoft Server (en-US, Guy24kRUS)"
es-ES | Espanhol (Espanha) |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Laura, Apollo)”
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, HelenaRUS)”
| | |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-ES, Pablo, Apollo)”
es-MX | Espanhol (México) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, HildaRUS)”
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (es-MX, Raul, Apollo)”
fi-FI | Finlandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fi-FI, HeidiRUS)”
fr-CA | Francês (Canadá) |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, Caroline)”
| | | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CA, HarmonieRUS)”
fr-CH | Francês (Suíça)|Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-CH, Guillaume)”
fr-FR | Francês (França)|Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Julie, Apollo)”
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, HortenseRUS)”
| | |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (fr-FR, Paul, Apollo)”
he-IL| Hebraico (Israel) | Masculino| “Voz da Conversão de Texto em Fala do Microsoft Server Speech (he-IL, Asaf)”
hi-IN | Hindi (Índia) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana, Apollo)”
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Kalpana)”
| | | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hi-IN, Hemant)”
hr-HR | Croata | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hr-HR, Matej)”
hu-HU | Húngaro | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (hu-HU, Szabolcs)”
id-ID | Indonésio| Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (id-ID, Andika)”
it-IT | Italiano |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (it-IT, Cosimo, Apollo)”
| | |Feminino | "Conversão de Texto em Fala do Microsoft Server (it-IT, LuciaRUS)"
ja-JP | Japonês |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ayumi, Apollo)”
| | |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, Ichiro, Apollo)”
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ja-JP, HarukaRUS)”
ko-KR | Coreano |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ko-KR, HeamiRUS)”
ms-MY | Malaio | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ms-MY, Rizwan)”
nb-NO | Norueguês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nb-NO, HuldaRUS)”
nl-NL | Holandês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (nl-NL, HannaRUS)”
pl-PL | Polonês | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pl-PL, PaulinaRUS)”
pt-BR | Português (Brasil) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, HeloisaRUS)”
| | |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-BR, Daniel, Apollo)”
pt-PT | Português (Portugal) | Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (pt-PT, HeliaRUS)”
ro-RO | Romeno | Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ro-RO, Andrei)”
ru-RU |Russo| Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Irina, Apollo)”
| | |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ru-RU, Pavel, Apollo)”
| | |Feminino | "Conversão de Texto em Fala do Microsoft Server (ru-RU, EkaterinaRUS)"
sk-SK | Eslovaco|Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sk-SK, Filip)”
sl-SI | Esloveno|Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sl-SI, Lado)”
sv-SE | Sueco|Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (sv-SE, HedvigRUS)”
ta-IN | Tâmil (Índia) |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (ta-IN, Valluvar)”
te-IN | Télugo (Índia) |Feminino | "Voz da Conversão de Texto em Fala do Microsoft Server Speech (te-IN, Chitra)"
th-TH | Tailandês|Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (th-TH, Pattara)”
tr-TR |Turco| Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (tr-TR, SedaRUS)”
vi-VN | Vietnamita|Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (vi-VN, An)”
zh-CN | Chinês (continente)|Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, HuihuiRUS)”
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Yaoyao, Apollo)”
| | |Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-CN, Kangkang, Apollo)”
zh-HK | Chinês (Hong Kong)|Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Tracy, Apollo)”
| | |Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, TracyRUS)”
| || Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-HK, Danny, Apollo)”
zh-TW | Chinês (Taiwan)|Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Yating, Apollo)”
| || Feminino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, HanHanRUS)”
| || Masculino | “Voz da Conversão de Texto em Fala do Microsoft Server Speech (zh-TW, Zhiwei, Apollo)”

\* *ar-EG é compatível com MSA (árabe padrão moderno).*

### <a name="customization"></a>Personalização

A personalização de voz está disponível para inglês dos EUA (en-US), chinês continental (zh-CN), francês (fr-FR), alemão (de-DE) e italiano (it-IT).

> [!NOTE]
> O treinamento de voz em francês, alemão e italiano começa com um conjunto de dados com mais de 2.000 enunciados. Os modelos bilíngues chinês-inglês também têm suporte com um conjunto de dados inicial de mais de 2 mil enunciados.

## <a name="speech-translation"></a>Tradução de Fala

A API de **Tradução de Fala** é compatível com diferentes idiomas para conversão de fala em fala e de fala em texto. O idioma de origem precisa sempre ser da seguinte tabela de idiomas de fala. Os idiomas de destino disponíveis dependem de se o destino de tradução é fala ou texto. Você pode traduzir a entrada de fala em mais de [60 idiomas](https://www.microsoft.com/translator/business/languages/). Um subconjunto desses idiomas está disponível para [síntese de fala](language-support.md#text-languages). 

### <a name="speech-languages"></a>Idiomas de fala

| Idioma de fala   | Código de idioma |
|:----------- |-|
| Árabe (padrão moderno)      | `ar` |
| Chinês (mandarim)      | `zh` |
| Inglês      | `en` |
| Francês      | `fr` |
| Alemão      | `de` |
| Italiano      | `it` |
| Japonês      | `jp` |
| Português (brasileiro)     | `pt` |
| Russo      | `ru` |
| Espanhol      |  `es` |

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

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
