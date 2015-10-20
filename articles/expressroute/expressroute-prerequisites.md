<properties
   pageTitle="Pré-requisitos para a adoção de Rota Expressa| Microsoft Azure"
   description="Esta página fornece uma lista dos requisitos a serem atendidos antes que você pode solicitar um circuito de Rota Expressa do Azure."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2015"
   ms.author="cherylmc"/>


# Pré-requisitos da Rota Expressa   

Para se conectar aos serviços de nuvem da Microsoft usando a Rota Expressa, será necessário verificar se os requisitos listados nas seções abaixo foram atendidos.

## Requisitos de conta

- Uma conta válida e ativa do Microsoft Azure. Isso é necessário para configurar o circuito da Rota Expressa. Os circuitos da Rota Expressa são recursos das assinaturas do Azure. Uma assinatura do Azure será um requisito mesmo se a conectividade estiver limitada a serviços de nuvem da Microsoft que não sejam do Azure, como os serviços do Office 365 e o CRM online.
- Uma assinatura ativa do Office 365 (se estiver usando os serviços do Office 365). Consulte a seção [Requisitos específicos do Office 365](#office-365-specific-requirements) deste artigo para saber mais.

## Relação do provedor de conectividade

- Uma relação com um provedor de conectividade da lista de provedores com suporte através do qual a conectividade precisa ser facilitada. Você deve ter uma relação comercial existente com seu provedor de conectividade. Será necessário ter certeza de que o serviço que você tem com o provedor de conectividade é compatível com a Rota Expressa.
- Se você quiser usar um provedor de conectividade que não esteja na lista de provedores com suporte, ainda poderá criar uma conexão com os serviços de nuvem da Microsoft por meio de uma troca.
	- Verifique com seu provedor de conectividade se ele está presente em algum dos locais de troca que aparecem na lista de provedores com suporte.
	- Faça com que seu provedor de conectividade estenda sua rede para o local de troca à sua escolha.
	- Solicite um circuito da Rota Expressa com a troca como o provedor de conectividade.

## Conectividade física entre sua rede e o provedor de conectividade

Consulte a seção de modelos de conectividade para obter detalhes sobre os modelos de conectividade. Os clientes devem garantir que sua infraestrutura local esteja conectada fisicamente à infraestrutura do provedor de serviços por meio de um dos modelos descritos.

## Requisitos de redundância para a conectividade

Não há qualquer requisito de redundância de conectividade física entre a infraestrutura do cliente e a infraestrutura do provedor de serviços. A Microsoft exige redundância na Camada 3. A Microsoft exige a configuração do roteamento redundante entre a borda da Microsoft e a rede do cliente por meio do provedor de serviços para que cada um dos emparelhamentos seja habilitado. Se as sessões de roteamento não forem configuradas de maneira redundante, o SLA de disponibilidade de serviço será anulado.

## Considerações sobre endereços IP e roteamento

Os Provedores de Clientes/Conectividade são responsáveis pela configuração de sessões BGP redundantes com a infraestrutura de borda da Microsoft. Os clientes que optarem por se conectar por meio de provedores de VPN IP normalmente dependerão dos provedores de conectividade para gerenciar as configurações de roteamento. Os clientes colocalizados em um Exchange ou conectando-se à Microsoft por meio de um provedor de Ethernet ponto a ponto terão que configurar sessões BGP redundantes por emparelhamento a fim de atenderem aos requisitos de disponibilidade do SLA. Os provedores de conectividade podem oferecer isso como um serviço de valor agregado. Consulte a tabela de domínios de roteamento no artigo [Circuitos e domínios de roteamento da Rota Expressa](expressroute-circuit-peerings.md) para saber mais sobre os limites.

## Segurança e firewall

Consulte o documento [Serviços de Nuvem da Microsoft e segurança de rede ](../best-practices-network-security.md) para saber mais sobre segurança e firewall.

## Configuração de NAT para emparelhamentos públicos do Azure e da Microsoft

Consulte [Requisitos de NAT da Rota Expressa](expressroute-nat.md) para obter orientações detalhadas sobre requisitos e configurações. Verifique se seu provedor de conectividade gerenciará a configuração e o gerenciamento de NAT para você. Normalmente, os provedores de conectividade de Camada 3 gerenciarão a NAT para você.

## Requisitos específicos do Office 365

Examine os recursos a seguir para saber mais sobre os requisitos do Office 365.

- [Planejamento de rede e ajuste de desempenho para o Office 365](http://aka.ms/tune)
- [Gerenciamento de tráfego de rede do Office 365](https://msft.spoppe.com/teams/cpub/teams/IW_Admin/modsquad/_layouts/15/WopiFrame.aspx?sourcedoc=%7b23f09224-0668-4476-8627-aaff30931439%7d&action=edit&source=https%3A%2F%2Fmsft%2Espoppe%2Ecom%2Fteams%2Fcpub%2Fteams%2FIW%5FAdmin%2Fmodsquad%2FSitePages%2FHome%2Easpx)
- Consulte o artigo [Requisitos de Qualidade do Serviço (QoS) da Rota Expressa](expressroute-qos.md) para obter orientações detalhadas sobre os requisitos e configurações de QoS. Verifique se seu provedor de conectividade oferece várias classes de serviço para sua VPN. 

## Próximas etapas

- Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).
- Encontrar um provedor de serviços. Consulte [Parceiros e locais de emparelhamento da Rota Expressa](expressroute-locations.md).
- Consulte requisitos de [Roteamento](expressroute-routing.md), de [NAT](expressroute-nat.md) e de [QoS](expressroute-qos.md).
- Configurar sua conexão da Rota Expressa.
	- [Criar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md)
	- [Configurar o roteamento](expressroute-howto-routing-classic.md)
	- [Vincular uma rede virtual a um circuito da Rota Expressa](expressroute-howto-linkvnet-classic.md)

<!---HONumber=Oct15_HO3-->