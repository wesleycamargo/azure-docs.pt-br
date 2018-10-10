---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f4a1937062f7e59cb3ef3f38610e077e8d36a3ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47005539"
---
### <a name="what-is-expressroute-direct"></a>O que é o ExpressRoute Direct?

O ExpressRoute Direct fornece aos clientes a capacidade de conectar-se diretamente à rede global da Microsoft em locais de emparelhamento distribuídos estrategicamente no mundo todo. O ExpressRoute Direct fornece a conectividade dupla de 100 Gbps, que dá suporte à conectividade ativa/ativa em escala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Como os clientes se conectam ao ExpressRoute Direct? 

Os clientes precisarão trabalhar com suas operadoras locais e fornecedores de colocalização para obter conectividade com os roteadores do ExpressRoute para usar o ExpressRoute Direct.

### <a name="what-locations-will-the-100gbps-expressroute-direct-be-available-for-public-preview"></a>Em quais locais o ExpressRoute Direct de 100 Gbps estará disponível como Versão Prévia Pública? 

Um número seleto de locais de emparelhamento do ExpressRoute dará suporte a isso na versão prévia pública. As portas disponíveis serão dinâmicas e estarão disponíveis pelo PowerShell para exibir a capacidade. Os locais incluem e *estão sujeitos a alterações com base na disponibilidade*:

* Amsterdã
* Camberra
* Chicago
* Washington, D.C.
* Dallas 
* Hong Kong
* Los Angeles
* Cidade de Nova York
* Paris
* San Antonio
* Vale do Silício
* Cingapura 

### <a name="what-is-the-sla-for-expressroute-direct"></a>O que é o SLA para ExpressRoute Direct?

O ExpressRoute Direct utilizará o mesmo [nível empresarial do ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quais cenários os clientes devem considerar com o ExpressRoute Direct?  

O ExpressRoute Direct oferece aos clientes pares de portas diretas de 100 Gbps no backbone global da Microsoft. Os cenários que oferecerão os melhores benefícios aos clientes incluem: ingestão de dados em massa, isolamento físico para mercados regulados e capacidade dedicada para cenários intermitentes, como renderização. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual é o modelo de cobrança para ExpressRoute Direct? 

O ExpressRoute Direct será cobrado um valor fixo pelo par de portas. Circuitos padrão serão incluídos sem horas adicionais e o premium terá um pequeno encargo por complemento. A saída será cobrada por circuito na zona do local de emparelhamento.