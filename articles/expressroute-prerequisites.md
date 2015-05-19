<properties 
   pageTitle="Pré-requisitos para a adoção de Rota Expressa"
   description="Esta página fornece uma lista dos requisitos a serem atendidos antes que você pode solicitar um circuito de Rota Expressa"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2015"
   ms.author="cherylmc"/>


# Pré-requisitos para Rota Expressa  

Para se conectar aos serviços em nuvem da Microsoft usando Rota Expressa, você precisará verificar se os seguintes pré-requisitos foram atendidos:

## Pré-requisitos para conectividade

- Uma conta válida e ativa do Microsoft Azure
- Uma relação com um NSP \(provedor de serviços de rede\) ou um EXP \(provedor do Exchange\) da [lista de provedores com suporte](expressroute-locations.md) através do qual a conectividade precisa ser facilitada. Você deve ter uma relação comercial existente com o provedor de serviços de rede ou o provedor do Exchange. Você precisará certificar-se de que o serviço que você usa é compatível com a Rota Expressa. 
- Se você deseja usar um provedor de serviços de rede e seu provedor de serviços de rede não está na lista acima, você ainda pode criar uma conexão com o Azure. 
	- Verifique com seu provedor de rede para ver se eles estão presentes em qualquer um dos locais do Exchange listados acima.
	- Faça com que seu provedor de rede estenda sua rede para o local do Exchange de sua escolha.
	- Solicite um circuito de Rota Expressa por meio do provedor do Exchange para se conectar ao Azure.
- Conectividade para a infraestrutura do provedor de serviços. Você deve atender aos critérios para pelo menos um dos itens listados a seguir:
	- Você é um cliente VPN do provedor de serviços de rede e tem pelo menos um site local conectado à infraestrutura de VPN do provedor de serviços de rede. Entre em contato com seu provedor de serviços de rede para verificar se o seu serviço de VPN atende aos requisitos para a Rota Expressa
	- Sua infraestrutura está colocalizada no datacenter do provedor do Exchange.
	- Você tem conectividade Ethernet com a infraestrutura Exchange Ethernet do provedor do Exchange.	
- Endereços IP e números AS para configuração de roteamento. 
	- Você pode usar números AS privados para se conectar ao domínio de roteamento de emparelhamento privado do Azure. Se você optar por fazer isso, ele deve ser \> 65.000. Para obter mais informações sobre números AS, consulte [Números AS \(Sistema Autônomo\)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- Endereços IP para configurar rotas. Uma sub-rede /28 é necessária. Ela não pode se sobrepor a quaisquer intervalos de endereços IP usados em suas instalações locais ou no Azure.
	- Você deve usar seus próprios números AS públicos para configuração de sessões BGP com serviços públicos do Azure.

## Próximas etapas

- Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).
- Para obter informações sobre como configurar sua conexão de Rota Expressa, consulte 
	- [Configurar uma conexão de Rota Expressa por meio de um provedor de serviços de rede](expressroute-configuring-nsps.md)
	- [Configurar uma conexão de Rota Expressa por meio de um provedor do Exchange](expressroute-configuring-exps.md)
<!--HONumber=54-->