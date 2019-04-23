---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 6a0d84a2aaf4b52c7fb0163231574eaea63c18c3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803210"
---
### <a name="what-is-expressroute-direct"></a>O que é o ExpressRoute Direct?

O ExpressRoute Direct fornece aos clientes a capacidade de conectar-se diretamente à rede global da Microsoft em locais de emparelhamento distribuídos estrategicamente no mundo todo. O ExpressRoute Direct fornece a conectividade dupla de 100 Gbps, que dá suporte à conectividade ativa/ativa em escala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Como os clientes se conectam ao ExpressRoute Direct? 

Os clientes precisarão trabalhar com suas operadoras locais e fornecedores de colocalização para obter conectividade com os roteadores do ExpressRoute para usar o ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Locais que oferecem suporte atualmente diretos do ExpressRoute? 

As portas disponíveis serão dinâmicas e estarão disponíveis pelo PowerShell para exibir a capacidade. Os locais incluem e *estão sujeitos a alterações com base na disponibilidade*:

* Amsterdã
* Camberra
* Chicago
* Washington, D.C.
* Dallas 
* RAE de Hong Kong
* Los Angeles
* Cidade de Nova York
* Paris
* San Antonio
* Vale do Silício
* Singapura 

### <a name="what-is-the-sla-for-expressroute-direct"></a>O que é o SLA para ExpressRoute Direct?

O ExpressRoute Direct utilizará o mesmo [nível empresarial do ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quais cenários os clientes devem considerar com o ExpressRoute Direct?  

O ExpressRoute Direct oferece aos clientes pares de portas diretas de 100 Gbps no backbone global da Microsoft. Os cenários que fornecerão aos clientes os maiores benefícios incluem: Ingestão de dados em massa, isolamento físico para mercados regulados e capacidade dedicada para cenários intermitentes, como renderização. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual é o modelo de cobrança para ExpressRoute Direct? 

O ExpressRoute Direct será cobrado um valor fixo pelo par de portas. Circuitos padrão serão incluídos sem horas adicionais e o premium terá um pequeno encargo por complemento. A saída será cobrada por circuito na zona do local de emparelhamento.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Quando é o início de cobrança para os pares de porta direta ExpressRoute?

Os pares de portas do ExpressRoute Direct são cobrados 45 dias a partir da criação do recurso ou quando um ou os dois links são habilitados, o que vier primeiro. O período de cortesia de 45 dias é concedido para permitir aos clientes concluir o processo de conexão cruzada com o provedor de colocação.
