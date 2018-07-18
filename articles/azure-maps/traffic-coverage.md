---
title: Cobertura de tráfego nos Mapas do Azure | Microsoft Docs
description: Saiba mais sobre a cobertura de tráfego nos Mapas do Azure
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 49e91287d23c35d8cfea858d41c47ba15efd820c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="azure-maps-traffic-coverage"></a>Cobertura de tráfego dos Mapas do Azure

Mapas do Azure fornece informações de tráfego avançadas na forma **fluxo** e **incidentes** de tráfego. Esses dados podem ser visualizados em mapas ou usados para gerar rotas mais inteligentes que fatores em condições reais de direção. 

No entanto, Mapas não têm o mesmo nível de informações e precisão para todas as regiões. A tabela a seguir fornece informações sobre quais tipos de informações de tráfego que você pode solicitar de cada região: 

|Região  |Incidentes  |Flow  |
|---------|:---------:|:---------:|
|Argentina      |         |✓         |
|Austrália     |✓         |✓        |
|Áustria     |✓         |✓         |
|Bahrein     |         |✓         |
|Bélgica     |✓         |✓         |
|Brasil     |✓         |✓         |
|Bulgária     |✓         |✓         |
|Canadá     |✓         |✓         |
|Chile     |✓         |✓         |
|Colômbia      |         |✓         |
|Croácia     |         |✓         |
|República Tcheca     |✓         |✓         |
|Dinamarca     |✓         |✓         |
|Egito     |         |✓         |
|Estônia     |         | ✓        |
|Finlândia     |✓         |✓         |
|+Ilhas Aland      |✓         |✓         |
|França     |✓         |✓         |
|+Monaco     |✓         |✓         |
|Alemanha     |✓         |✓         |
|Grécia     |✓         |✓         |
|RAE de Hong Kong     |✓         |✓         |
|Hungria     |✓         |✓         |
|Islândia     |         |✓         |
|Indonésia     |✓         |✓         |
|Irlanda (República da)     |✓         |✓         |
|Israel     |         |✓         |
|Itália     |✓         |✓        |
|+San Marino     |✓         |✓         |
|Kuwait     |✓         |✓         |
|Letônia     |         |✓         |
|Lesoto     |✓         |✓         |
|Lituânia     |         |✓         |
|Luxemburgo     |✓         |✓         |
|RAE de Macau     |         |✓         |
|Malásia     |✓         |✓         |
|Malta     |✓         |✓         |
|México     |✓         |✓         |
|Marrocos     |         |✓         |
|Países Baixos     |✓         |✓         |
|Nova Zelândia     |✓         |✓         |
|Noruega     |✓         |✓         |
|Omã     |         |✓         |
|Polônia     |✓         |✓         |
|Portugal     |✓         |✓         |
|+Açores e Madeira     |         |✓         |
|Catar     |         |✓         |
|Romênia     |         |✓         |
|Federação Russa     |✓         |✓         |
|Arábia Saudita     |✓         |✓         |
|Cingapura     |✓         |✓         |
|Eslováquia     |✓         |✓         |
|Eslovênia     |✓         |✓         |
|África do Sul     |✓         |✓         |
|Espanha     |✓         |✓         |
|+Andorra     |✓         |✓         |
|+Ilhas de Baleares     |✓         |✓         |
|+Ilhas Canárias     |✓         |✓         |
|+Gibraltar     |✓         |✓         |
|Suécia     |✓         |✓         |
|Suíça     |✓         |✓        |
|+Liechtenstein      |✓         |✓         |
|Taiwan     |✓         |✓        |
|Tailândia     |✓         |✓        |
|Turquia     |✓         |✓         |
|Ucrânia     |✓         |✓         |
|Emirados Árabes Unidos     |✓         |✓         |
|Reino Unido     |✓         |✓         |
|(Guernsey & Jersey)     |✓         |✓         |
|Ilha de Man     |✓         |✓         |
|Estados Unidos     |✓         |✓        |
|+Porto Rico     |✓         |✓         |

Para obter mais informações sobre os dados de tráfego de Mapas do Azure, consulte as páginas de referência [Tráfego](https://docs.microsoft.com/rest/api/maps/traffic).