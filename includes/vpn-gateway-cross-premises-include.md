---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/16/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55ac7e055c972a9b18ef374ac8498b418c5d56af
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34307576"
---
|  | **Ponto a Site** | **Site a site** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Serviços com Suporte no Azure** |Serviços de Nuvem e Máquinas Virtuais |Serviços de Nuvem e Máquinas Virtuais |[Lista de serviços](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Larguras de Banda Típicas** |Com base na SKU do gateway |Normalmente < 1 Gbps agregado |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps e 10 Gbps |
| **Protocolos com Suporte** |SSTP (Secure Sockets Tunneling Protocol) e IPsec |IPsec |Conexão direta pelas tecnologias VLANs e VPN do NSP (MPLS, VPLS...) |
| **Roteamento** |RouteBased (dinâmico) |Damos suporte a PolicyBased (roteamento estático) e RouteBased (VPN de roteamento dinâmico) |BGP |
| **Resiliência de conexão** |ativo-passivo |ativo-passivo ou ativo-ativo |ativo-ativo |
| **Caso de uso típico** |Criação de protótipos, cenários de desenvolvimento / teste / laboratório para serviços de nuvem e máquinas virtuais |Cenários de desenvolvimento / teste / laboratório e de cargas de trabalho de produção em pequena escala para serviços de nuvem e máquinas virtuais |Acesso a todos os serviços do Azure (lista validada), cargas de trabalho essenciais e em nível Empresarial, Backup, Big Data e Azure como um site de DR |
| **CONTRATO DE NÍVEL DE SERVIÇO** |[CONTRATO DE NÍVEL DE SERVIÇO](https://azure.microsoft.com/support/legal/sla/) |[CONTRATO DE NÍVEL DE SERVIÇO](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Preços** |[Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Preços](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Documentação Técnica** |[Documentação de Gateway de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentação de Gateway de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **PERGUNTAS FREQUENTES** |[Perguntas frequentes de gateway de VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Perguntas frequentes de gateway de VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Perguntas Frequentes sobre o ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
