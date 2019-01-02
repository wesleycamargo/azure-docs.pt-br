---
title: Traduzir atrás de firewalls – API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Traduzir atrás de firewalls de IP com a API de Tradução de Texto.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: bf1a63d996a1ed2021654c96576eb9dac2cff8e6
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52683329"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Como traduzir atrás de firewalls de IP com a API de Tradução de Texto

A API de Tradução de Texto pode ser traduzida por trás de firewalls usando filtragem de IP ou nome de domínio. A filtragem de nome de domínio é o método preferencial. **Não é recomendável** executar o Microsoft Translator por trás de um firewall filtrado por IP. A configuração é suscetível a interrupção no futuro sem aviso prévio. 

## <a name="translator-ip-addresses"></a>Endereços IP do Tradutor
Os endereços IP para api.cognitive.microsofttranslator.com – API de Tradução de Texto da Microsoft a partir de 20 de novembro de 2018:

* **Pacífico Asiático:** 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99
* **América do Norte** 40.90.139.36, 40.90.139.2


## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Traduzir atrás de firewalls de IP na chamada à API do Tradutor](reference/v3-0-translate.md)