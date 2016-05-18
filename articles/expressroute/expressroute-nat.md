<properties
   pageTitle="Requisitos de NAT para circuitos da Rota Expressa | Microsoft Azure"
   description="Esta página fornece os requisitos detalhados para a configuração e o gerenciamento de NAT para circuitos da Rota Expressa."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/18/2016"
   ms.author="cherylmc"/>

# Requisitos de NAT da Rota Expressa

Para se conectar aos serviços de nuvem da Microsoft usando a Rota Expressa, você precisará configurar e gerenciar NATs. Alguns provedores de conectividade oferecem a configuração e o gerenciamento de NAT como um serviço gerenciado. Verifique se seu provedor de conectividade oferece esse serviço. Caso contrário, você deverá atender aos requisitos descritos abaixo.

Consulte a página [Circuitos e domínios de roteamento da Rota Expressa](expressroute-circuit-peerings.md) para obter uma visão geral dos vários domínios de roteamento. Para atender aos requisitos de endereço IP público para o emparelhamento público do Azure e da Microsoft, recomendamos a configuração de NAT entre sua rede e a Microsoft. Esta seção fornece uma descrição detalhada da infraestrutura NAT que precisa ser configurada.

## Requisitos de NAT para o emparelhamento público do Azure

O caminho do emparelhamento público do Azure permite que você se conecte a todos os serviços hospedados no Azure por meio de seus endereços IP públicos. Entre eles estão todos os serviços listados nas [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) e quaisquer serviços hospedados por ISVs no Microsoft Azure. A conectividade com os serviços do Microsoft Azure no emparelhamento público é sempre iniciada de sua rede para a rede da Microsoft. O tráfego destinado ao Microsoft Azure no emparelhamento público deve estar no modo SNAT para endereços IPv4 públicos válidos antes que eles entrem na rede da Microsoft. A figura abaixo fornece uma imagem de alto nível de como a NAT deve ser configurada para atender ao requisito acima.

![](./media/expressroute-nat/expressroute-nat-azure-public.png)

### Pool de IP de NAT e anúncios de rota

Você deve garantir que o tráfego esteja entrando no caminho de emparelhamento público do Azure com o endereço IPv4 público válido. A Microsoft deve ser capaz de validar a propriedade do pool de endereços NAT IPv4 com base em um registro regional de roteamento da Internet (RIR) ou em um registro de roteamento da Internet (IRR). Uma verificação será executada com base no número de AS emparelhados e nos endereços IP usados para NAT. Consulte a página [Requisitos de roteamento da Rota Expressa](expressroute-routing.md) para obter informações sobre registros de roteamento.
 
Não há restrições com relação ao tamanho do prefixo IP de NAT anunciado por meio desse emparelhamento. Você deve monitorar o pool de NAT e não ficar sem sessões NAT.

>[AZURE.IMPORTANT] O pool de IPs de NAT anunciado à Microsoft não deve ser anunciado na Internet. Isso interromperá a conectividade com outros serviços da Microsoft.

## Requisitos de NAT para emparelhamento da Microsoft

O caminho de emparelhamento da Microsoft permite que você se conecte a serviços de nuvem da Microsoft que não têm suporte por meio do caminho de emparelhamento público do Azure. A lista de serviços inclui serviços do Office 365, como o Exchange Online, o SharePoint Online, o Skype for Business e o CRM Online. A Microsoft espera dar suporte à conectividade bidirecional no emparelhamento da Microsoft. O tráfego destinado aos serviços de nuvem da Microsoft deve estar no modo SNAT para endereços IPv4 públicos válidos antes de entrar na rede da Microsoft. O tráfego destinado a sua rede proveniente dos serviços de nuvem da Microsoft deve estar no modo SNAT antes de entrar na rede. A figura abaixo fornece uma imagem de alto nível de como o NAT deve ser configurado para emparelhamento da Microsoft.
 
![](./media/expressroute-nat/expressroute-nat-microsoft.png)


#### Tráfego com origem em sua rede e destinado à Microsoft

- Você deve garantir que o tráfego esteja entrando no caminho de emparelhamento da Microsoft com um endereço IPv4 público válido. A Microsoft deve ser capaz de validar a propriedade do pool de endereços NAT IPv4 com base no registro regional de roteamento da Internet (RIR) ou em um registro de roteamento da Internet (IRR). Uma verificação será executada com base no número de AS emparelhados e nos endereços IP usados para NAT. Consulte a página [Requisitos de roteamento da Rota Expressa](expressroute-routing.md) para obter informações sobre registros de roteamento.

- Os endereços IP usados para a configuração do emparelhamento público do Azure e de outros circuitos de Rota Expressa não devem ser anunciados à Microsoft por meio da sessão BGP. Não há restrições com relação ao tamanho do prefixo IP NAT anunciado por meio desse emparelhamento.

	>[AZURE.IMPORTANT] O pool de IPs de NAT anunciado à Microsoft não deve ser anunciado na Internet. Isso interromperá a conectividade com outros serviços da Microsoft.

#### Tráfego com origem na Microsoft e destinado à sua rede

- Certas situações exigem que a Microsoft inicie a conectividade com os pontos de extremidade do serviço hospedados em sua rede. Um exemplo típico dessa situação seria a conectividade com servidores ADFS hospedados em sua rede desde o Office 365. Nesses casos, você deve vazar os prefixos apropriados de sua rede para o emparelhamento da Microsoft. 

- Você deve colocar no modo SNAT o tráfego destinado aos endereços IP em sua rede da Microsoft.

## Próximas etapas

- Consulte os requisitos de [Roteamento](expressroute-routing.md) e de [QoS](expressroute-qos.md).
- Para obter informações sobre o fluxo de trabalho, consulte [Fluxos de trabalho de provisionamento e estados do circuito da Rota Expressa](expressroute-workflows.md)
- Configurar sua conexão da Rota Expressa.

	- [Criar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md)
	- [Configurar o roteamento](expressroute-howto-routing-classic.md)
	- [Vincular uma rede virtual a um circuito da Rota Expressa](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0420_2016-->