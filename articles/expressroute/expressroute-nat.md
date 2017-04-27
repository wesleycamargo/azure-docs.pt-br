---
title: Requisitos de NAT para circuitos da ExpressRoute | Microsoft Docs
description: "Esta página fornece os requisitos detalhados para a configuração e o gerenciamento de NAT para circuitos da Rota Expressa."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 867bf936-c851-485f-84c8-d8d6e33fee9f
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a7b3f8addbba21e60be0076784ae954f4cedb0b8
ms.lasthandoff: 04/17/2017


---
# <a name="expressroute-nat-requirements"></a>Requisitos de NAT da Rota Expressa
Para se conectar aos serviços de nuvem da Microsoft usando a Rota Expressa, você precisará configurar e gerenciar NATs. Alguns provedores de conectividade oferecem a configuração e o gerenciamento de NAT como um serviço gerenciado. Verifique se seu provedor de conectividade oferece esse serviço. Caso contrário, você deverá atender aos requisitos descritos abaixo. 

Consulte a página [Circuitos e domínios de roteamento da Rota Expressa](expressroute-circuit-peerings.md) para obter uma visão geral dos vários domínios de roteamento. Para atender aos requisitos de endereço IP público para o emparelhamento público do Azure e da Microsoft, recomendamos a configuração de NAT entre sua rede e a Microsoft. Esta seção fornece uma descrição detalhada da infraestrutura NAT que precisa ser configurada.

## <a name="nat-requirements-for-azure-public-peering"></a>Requisitos de NAT para o emparelhamento público do Azure
O caminho do emparelhamento público do Azure permite que você se conecte a todos os serviços hospedados no Azure por meio de seus endereços IP públicos. Isso inclui os serviços listados nas [Perguntas Frequentes sobre a Rota Expressa](expressroute-faqs.md) e quaisquer serviços hospedados por ISVs no Microsoft Azure. 

> [!IMPORTANT]
> A conectividade com os serviços do Microsoft Azure no emparelhamento público é sempre iniciada de sua rede para a rede da Microsoft. Portanto, as sessões não podem ser iniciadas dos serviços do Microsoft Azure para sua rede por ExpressRoute. Se você tentar, os pacotes enviados aos IPs indicados usarão a Internet em vez do ExpressRoute.
> 

O tráfego destinado ao Microsoft Azure no emparelhamento público deve estar no modo SNAT para endereços IPv4 públicos válidos antes que eles entrem na rede da Microsoft. A figura abaixo fornece uma imagem de alto nível de como a NAT deve ser configurada para atender ao requisito acima.

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>Pool de IP de NAT e anúncios de rota
Você deve garantir que o tráfego esteja entrando no caminho de emparelhamento público do Azure com o endereço IPv4 público válido. A Microsoft deve ser capaz de validar a propriedade do pool de endereços NAT IPv4 com base em um registro regional de roteamento da Internet (RIR) ou em um registro de roteamento da Internet (IRR). Uma verificação será executada com base no número de AS emparelhados e nos endereços IP usados para NAT. Consulte a página [Requisitos de roteamento da Rota Expressa](expressroute-routing.md) para obter informações sobre registros de roteamento.

Não há restrições com relação ao tamanho do prefixo IP de NAT anunciado por meio desse emparelhamento. Você deve monitorar o pool de NAT e não ficar sem sessões NAT.

> [!IMPORTANT]
> O pool de IPs de NAT anunciado à Microsoft não deve ser anunciado na Internet. Isso interromperá a conectividade com outros serviços da Microsoft.
> 
> 

## <a name="nat-requirements-for-microsoft-peering"></a>Requisitos de NAT para emparelhamento da Microsoft
O caminho de emparelhamento da Microsoft permite que você se conecte a serviços de nuvem da Microsoft que não têm suporte por meio do caminho de emparelhamento público do Azure. A lista de serviços inclui serviços do Office 365, como o Exchange Online, o SharePoint Online, o Skype for Business e o CRM Online. A Microsoft espera dar suporte à conectividade bidirecional no emparelhamento da Microsoft. O tráfego destinado aos serviços de nuvem da Microsoft deve estar no modo SNAT para endereços IPv4 públicos válidos antes de entrar na rede da Microsoft. O tráfego destinado à sua rede proveniente dos serviços de nuvem da Microsoft deve estar no modo SNAT antes de entrar na rede. A figura abaixo fornece uma imagem de alto nível de como o NAT deve ser configurado para emparelhamento da Microsoft.

![](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Tráfego com origem em sua rede e destinado à Microsoft
* Você deve garantir que o tráfego esteja entrando no caminho de emparelhamento da Microsoft com um endereço IPv4 público válido. A Microsoft deve ser capaz de validar a propriedade do pool de endereços NAT IPv4 com base no registro regional de roteamento da Internet (RIR) ou em um registro de roteamento da Internet (IRR). Uma verificação será executada com base no número de AS emparelhados e nos endereços IP usados para NAT. Consulte a página [Requisitos de roteamento da Rota Expressa](expressroute-routing.md) para obter informações sobre registros de roteamento.
* Os endereços IP usados para a configuração do emparelhamento público do Azure e de outros circuitos de Rota Expressa não devem ser anunciados à Microsoft por meio da sessão BGP. Não há restrições com relação ao tamanho do prefixo IP NAT anunciado por meio desse emparelhamento.
  
  > [!IMPORTANT]
  > O pool de IPs de NAT anunciado à Microsoft não deve ser anunciado na Internet. Isso interromperá a conectividade com outros serviços da Microsoft.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Tráfego com origem na Microsoft e destinado à sua rede
* Certas situações exigem que a Microsoft inicie a conectividade com os pontos de extremidade do serviço hospedados em sua rede. Um exemplo típico dessa situação seria a conectividade com servidores ADFS hospedados em sua rede desde o Office 365. Nesses casos, você deve vazar os prefixos apropriados de sua rede para o emparelhamento da Microsoft. 
* Você deve colocar no modo SNAT o tráfego destinado aos endereços IP em sua rede da Microsoft. 

## <a name="next-steps"></a>Próximas etapas
* Consulte os requisitos para o [Roteamento](expressroute-routing.md) e [QoS](expressroute-qos.md).
* Para obter informações sobre o fluxo de trabalho, consulte [Fluxos de trabalho de provisionamento e estados do circuito da Rota Expressa](expressroute-workflows.md)
* Configurar sua conexão da Rota Expressa.
  
  * [Criar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md)
  * [Configurar o roteamento](expressroute-howto-routing-classic.md)
  * [Vincular uma Rede Virtual a um circuito de Rota Expressa](expressroute-howto-linkvnet-classic.md)


