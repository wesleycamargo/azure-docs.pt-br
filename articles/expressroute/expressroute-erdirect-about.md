---
title: Sobre o ExpressRoute Direct – Azure | Microsoft Docs
description: Esta página fornece uma visão geral do ExpressRoute direta
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: fb9dc5116ba23d57c7f2fe543e734759e8bbcc7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60367619"
---
# <a name="about-expressroute-direct"></a>Sobre o ExpressRoute Direct

O ExpressRoute Direct fornece a capacidade de conectar-se diretamente à rede global da Microsoft em localizações de emparelhamento estrategicamente distribuídas no mundo todo. O ExpressRoute Direct oferece conectividade dupla de 100 Gbps, que suporta conectividade ativa / ativa em escala.

Os principais recursos que o ExpressRoute Direct fornece incluem, mas não estão limitados a:

* Ingestão de dados em massa em serviços como Armazenamento e o Cosmos DB
* Isolamento físico para setores regulamentados e que requerem conectividade dedicada e isolada, como: bancos, governo e varejo
* Controle granular de distribuição de circuito com base em unidade de negócios

## <a name="onboard-to-expressroute-direct"></a>Integrar ao diretos do ExpressRoute

Antes de usar diretos do ExpressRoute, você deve primeiro registrar sua assinatura. Para se inscrever, envie um e-mail para <ExpressRouteDirect@microsoft.com> com seu ID de assinatura, incluindo os seguintes detalhes:

* Cenários que você pretende para realizar com o **ExpressRoute Direct**
* Preferências de localização. Confira [Localizações de emparelhamento e parceiros do ExpressRoute](expressroute-locations-providers.md) para obter uma lista completa de todas as localizações
* Linha do tempo para implementação
* Tem mais perguntas?

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute usando um provedor de serviços e ExpressRoute Direct

| **ExpressRoute usando um provedor de serviços** | **ExpressRoute Direct** | 
| --- | --- |
| Utiliza provedores de serviços para permitir integração e conectividade rápidas na infraestrutura existente | Requer infra-estrutura de 100 Gbps e gerenciamento total de todas as camadas
| Integra-se a centenas de fornecedores, inclusive Ethernet e MPLS | Capacidade direta/dedicada para setores regulamentados e ingestão de dados em massa |
| SKUs de circuitos de 50 Mbps a 10 Gbps | O cliente pode selecionar uma combinação dos seguintes SKUs de circuito: 5 Gbps, 10 Gbps, 40 Gbps, 100 Gbps – limitados a um total de 200 Gbps
| Otimizado para um único locatário | Otimizado para um único locatário/provedores de serviços de nuvem/várias unidades de negócios

## <a name="expressroute-direct-circuits"></a>Circuitos do ExpressRoute Direct

O Microsoft Azure ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o Dynamic 365.  

Cada localização de emparelhamento tem acesso à rede global da Microsoft e pode acessar qualquer região em uma zona geopolítica por padrão e todas as regiões globais com um circuito Premium.  

A funcionalidade na maioria dos cenários é equivalente a circuitos que utilizam um provedor de serviço do ExpressRoute para a operação. Para dar suporte a ainda mais granularidade e às novas funcionalidades oferecidas usando o ExpressRoute Direct, há determinadas funcionalidades principais que existem nos Circuitos do ExpressRoute Direct.

## <a name="circuit-skus"></a>SKUs de circuito

O ExpressRoute Direct dá suporte a cenários de ingestão de dados em massa no Armazenamento do Azure e em outros serviços de Big Data. Os circuitos do ExpressRoute no ExpressRoute Direct agora também dão suporte a SKUs de **40 Gbps** e **100 Gbps**. Os pares de portas físicas têm **100Gbps** apenas e podem ter vários circuitos virtuais com larguras de banda de 5, 10, 40, 100Gbps - até 200Gbps em qualquer combinação. 

## <a name="vlan-tagging"></a>Marcação de VLAN

O ExpressRoute Direct dá suporte à marcação de VLAN QinQ e Dot1Q.

* **Marcação de VLAN QinQ** permite domínios de roteamento isolados com base no circuito do ExpressRoute. O Azure aloca dinamicamente uma marca S na criação do circuito e isso não pode ser alterado. Cada emparelhamento no circuito (privado e Microsoft) usará uma marca C exclusiva como a VLAN. A marca C não precisa ser exclusiva nos circuitos nas portas do ExpressRoute Direct.

* **Marcação de VLAN Dot1Q** permite a marcação de uma única VLAN com base no par de portas do ExpressRoute Direct. Uma marca C usada em um emparelhamento precisa ser exclusiva em todos os circuitos e emparelhamentos do par de portas do ExpressRoute Direct.

## <a name="workflow"></a>Fluxo de trabalho

[![Fluxo de trabalho](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Contrato de Nível de Serviço

O ExpressRoute Direct fornece o mesmo SLA de nível empresarial com conexões redundantes ativas/ativas à rede global da Microsoft. A infraestrutura do ExpressRoute é redundante e a conectividade com a rede global da Microsoft é redundante, diversificada e pode ser dimensionada de acordo com os requisitos do cliente. 

## <a name="next-steps"></a>Próximas etapas

[Configurar o ExpressRoute Direct](expressroute-howto-erdirect.md)
