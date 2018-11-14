---
title: Sobre o Azure ExpressRoute Direct | Microsoft Docs
description: Esta página fornece uma visão geral do ExpressRoute Direct (versão prévia)
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: mialdrid
ms.openlocfilehash: 3b46f24edf10fee7001b35ffdd9fa218aa438e19
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959658"
---
# <a name="about-expressroute-direct-preview"></a>Sobre o ExpressRoute Direct (versão prévia)

O ExpressRoute Direct fornece aos clientes a capacidade de conectar-se diretamente à rede global da Microsoft em locais de emparelhamento distribuídos estrategicamente no mundo todo. O ExpressRoute Direct oferece conectividade dupla de 100 Gbps, que suporta conectividade ativa / ativa em escala.

Os principais recursos que o ExpressRoute Direct fornece incluem, mas não estão limitados a:

* Ingestão de dados em massa em serviços como Armazenamento e o Cosmos DB
* Isolamento físico para indústrias que são regulamentadas e exigem conectividade dedicada e isolada, como: bancos, governo e varejo
* Controle granular de distribuição de circuito com base em unidade de negócios

> [!IMPORTANT]
> O ExpressRoute Direct está na versão prévia no momento.
>
> Essa versão prévia pública é fornecida sem um contrato de nível de serviço e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="enroll-in-the-preview"></a>Registrar-se na versão prévia

Antes de usar o ExpressRoute Direct, você precisa registrar sua assinatura na versão prévia. Para se inscrever, envie um e-mail para <ExpressRouteDirect@microsoft.com> com seu ID de assinatura, incluindo os seguintes detalhes:

* Cenários que você pretende para realizar com o **ExpressRoute Direct**
* Preferências de localização. Confira [Localizações de emparelhamento e parceiros do ExpressRoute](expressroute-locations-providers.md) para obter uma lista completa de todas as localizações
* Linha do tempo para implementação
* Tem mais perguntas?

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute usando um provedor de serviços e ExpressRoute Direct

| **ExpressRoute usando um provedor de serviços** | **ExpressRoute Direct** | 
| --- | --- |
| Utiliza provedores de serviços para permitir integração e conectividade rápidas na infraestrutura existente | Requer infra-estrutura de 100 Gbps e gerenciamento total de todas as camadas
| Integra-se a centenas de fornecedores, inclusive Ethernet e MPLS | Capacidade direta/dedicada para setores regulamentados e ingestão de dados em massa |
| SKUs de circuitos de 50 Mbps a 10 Gbps | Cliente pode selecionar uma combinação do circuito seguinte SKUs: 5 Gbps, 10 Gbps, 40 Gbps, 100 Gbps - limitados a um total de 200 Gbps
| Otimizado para um único locatário | Otimizado para um único locatário/provedores de serviços de nuvem/várias unidades de negócios

## <a name="expressroute-direct-circuits"></a>Circuitos do ExpressRoute Direct

O Microsoft Azure ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o Dynamic 365.  

Cada localização de emparelhamento tem acesso à rede global da Microsoft e pode acessar qualquer região em uma zona geopolítica por padrão e todas as regiões globais com um circuito Premium.  

A funcionalidade na maioria dos cenários é equivalente a circuitos que utilizam um provedor de serviço do ExpressRoute para a operação. Para dar suporte a ainda mais granularidade e às novas funcionalidades oferecidas usando o ExpressRoute Direct, há determinadas funcionalidades principais que existem nos Circuitos do ExpressRoute Direct.

## <a name="circuit-skus"></a>SKUs de circuito

O ExpressRoute Direct dá suporte a cenários de ingestão de dados em massa no Armazenamento do Azure e em outros serviços de Big Data. Os circuitos do ExpressRoute no ExpressRoute Direct agora também dão suporte a SKUs de **40 Gbps** e **100 Gbps**.

## <a name="vlan-tagging"></a>Marcação de VLAN

O ExpressRoute Direct dá suporte à marcação de VLAN QinQ e Dot1Q.

* **Marcação de VLAN QinQ** permite domínios de roteamento isolados com base no circuito do ExpressRoute. O Azure aloca dinamicamente uma marca S na criação do circuito e isso não pode ser alterado. Cada emparelhamento no circuito (privado e Microsoft) usará uma marca C exclusiva como a VLAN. A marca C não precisa ser exclusiva nos circuitos nas portas do ExpressRoute Direct.

* **Marcação de VLAN Dot1Q** permite a marcação de uma única VLAN com base no par de portas do ExpressRoute Direct. Uma marca C usada em um emparelhamento precisa ser exclusiva em todos os circuitos e emparelhamentos do par de portas do ExpressRoute Direct.

## <a name="workflow"></a>Fluxo de trabalho

![fluxo de trabalho](./media/expressroute-erdirect-about/workflow1.png)

## <a name="sla"></a>Contrato de Nível de Serviço

O ExpressRoute Direct fornece o mesmo SLA de nível empresarial com conexões redundantes ativas/ativas à rede global da Microsoft. A infraestrutura do ExpressRoute é redundante e a conectividade com a rede global da Microsoft é redundante, diversificada e pode ser dimensionada de acordo com os requisitos do cliente. Durante a versão prévia, não haverá nenhum SLA e o serviço deverá ser considerado apenas para cargas de trabalho que não sejam de produção.

## <a name="next-steps"></a>Próximas etapas

[Configurar o ExpressRoute Direct](expressroute-howto-erdirect.md)
