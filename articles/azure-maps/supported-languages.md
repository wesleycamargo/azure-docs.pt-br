---
title: Suporte de localização em mapas do Azure | Microsoft Docs
description: Saiba mais sobre idiomas com suporte para os serviços nos Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 1928185521419006a487a933e2ecba79894a09d3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686777"
---
# <a name="localization-support-in-azure-maps"></a>Suporte de localização em mapas do Azure

Mapas do Azure dá suporte a vários idiomas e modos de exibição com base no país/região. Este artigo fornece as exibições para ajudar a orientar a implementação de mapas do Azure e os idiomas com suporte.


## <a name="azure-maps-supported-languages"></a>Idiomas com suporte nos Mapas do Azure

Os Mapas do Azure foram traduzidos para variedade de idiomas nos serviços. A tabela a seguir fornece os códigos de idioma com suporte para cada serviço.  
  

| ID         | NOME                   |  Mapas | Search | Roteamento | Incidentes de tráfego | Controle de mapa JS | Fuso horário |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| af-ZA      | Africâner              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Árabe                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| eu-ES      | Basco                 |       |    ✓   |         |                   |                |     ✓     |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ca-ES      | Catalão                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Chinês (Simplificado)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Chinês (Tradicional)  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| hr-HR      | Croata               |       |    ✓   |         |                   |                |     ✓     |
| cs-CZ      | Tcheco                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Dinamarquês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-NL      | Holandês                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | Holandês (Bélgica)        |       |    ✓   |         |                   |                |     ✓     |
| en-AU      | Inglês (Austrália)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-NZ      | Inglês (Nova Zelândia)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Inglês (Grã-Bretanha) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Inglês (EUA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estoniano               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Finlandês                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Francês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Francês (canadense)      |       |    ✓   |         |                   |                |     ✓     |
| gl-ES      | Galego               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Alemão                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el-GR      | Grego                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| he-IL      | Hebraico                 |       |    ✓   |         |         ✓         |                |     ✓     |
| hi-IN      | Híndi                  |       |        |         |                   |                |     ✓     |
| hu-HU      | Húngaro              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japonês               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Cazaque                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Espanhol (América Latina) |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | Letão                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ms-MY      | Malaio (latino)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| nb-NO      | Norueguês bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutral Ground Truth - idiomas oficiais para todas as regiões em scripts locais, se disponível |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Neutro verdade - exônimos latinos. Script latino será usado se disponível |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | Polonês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ro-RO      | Romeno               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | Sérvio (cirílico)     |       |    Sérvio (cirílico) (sr-RS)   |         |                   |                |     ✓     |
| sr-Latn-RS | Sérvio (latino)        |       |        |         |                   |                |     ✓     |
| sk-SK      | Eslovaco              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sl-SL      | Esloveno              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Espanhol                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | Espanhol (México)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv -SE     | Sueco                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th-TH      | Tailandês                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| uk-UA      | Ucraniano               |       |    ✓   |         |                   |                |     ✓     |
| vi-VN      | Vietnamita             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Mapas do Azure com suporte a modos de exibição

Parâmetro de mapas de exibição do Azure (também chamado de "parâmetro de região do usuário") é um código de país ISO 3166 2 letras que mostrará o maps correto para esse país/região especificando qual conjunto de geopoliticamente questionadas bordas e rótulos são exibidos no mapa.  Por padrão, o parâmetro de modo de exibição é definido como **"Unificada"**.  País/regiões que não estão na lista de exibição padrão será o modo de exibição "Unified". É sua responsabilidade para determinar o local dos seus usuários e, em seguida, defina o parâmetro View corretamente para esse local. O parâmetro de modo de exibição em mapas do Azure deve ser usado em conformidade com as leis aplicáveis, incluindo aquelas sobre mapeamento do país em que os mapas, imagens e outros conteúdos de terceiros e dados que você está autorizado a acessar por meio do Azure mapas é disponibilizado.

A tabela a seguir fornece exibições com suporte.

| Visualizar         | DESCRIÇÃO                            |  Mapas | Search | Controle de mapa JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (exibição árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (exibição Argentinian)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (exibição árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (exibição Indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (exibição árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (exibição árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (exibição árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (exibição árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (exibição Maroko)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (exibição árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (exibição paquistanesa)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestina (exibição árabe)    |   ✓   |        |     ✓          |
| QA           | Catar (exibição árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (exibição árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (exibição árabe)                   |   ✓   |        |     ✓          |
| YE           | Iêmen (exibição árabe)                   |   ✓   |        |     ✓          |
| Unificado      | Exibição unificada (outros)                  |   ✓   |   ✓     |     ✓          |
