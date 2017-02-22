---
title: Perguntas frequentes sobre o Azure ExpressRoute | Microsoft Docs
description: "As Perguntas Frequentes Sobre Rota Expressa contêm informações sobre Serviços do Azure com Suporte, Custo, Dados e Conexões, SLA, Provedores e Locais, Largura de banda e Detalhes Técnicos adicionais."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 239702c8b099dd422e6b67a267b1185a27a21807
ms.openlocfilehash: 52d9194920019291696d5ace3ac24751fde674ab


---
# <a name="expressroute-faq"></a>Perguntas Frequentes sobre Rota Expressa
## <a name="what-is-expressroute"></a>O que é Rota Expressa?
Rota Expressa é um serviço do Azure que permite a criação de conexões privadas entre os datacenters da Microsoft e a infraestrutura no seu local ou em uma instalação de colocalização. As conexões de ExpressRoute não passam pela Internet pública e oferecem mais segurança, confiabilidade e velocidades maiores com latências menores do que conexões típicas pela Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Quais são os benefícios do uso de Rota Expressa e conexões de rede privada?
As conexões de ExpressRoute não passam pela Internet pública e oferecem mais segurança, confiabilidade e velocidades maiores com latências consistentes e menores do que conexões típicas pela Internet. Em alguns casos, o uso de conexões da Rota Expressa para transferir dados entre dispositivos locais e o Azure pode proporcionar relações custo/benefício significativas.

### <a name="what-microsoft-cloud-services-are-supported-over-expressroute"></a>Quais serviços em nuvem da Microsoft têm suporte na Rota Expressa?
A Rota Expressa dá suporte à maioria dos serviços do Microsoft Azure atualmente, incluindo o Office 365.  Procure por atualizações com disponibilidade geral, em breve.

### <a name="where-is-the-service-available"></a>Onde o serviço está disponível?
Consulte esta página para localização de serviço e disponibilidade: [Locais e Parceiros da Rota Expressa](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Como posso usar a Rota Expressa para conexão à Microsoft se eu não tenho parcerias com uma das operadoras parceiras da Rota Expressa?
Você pode selecionar uma operadora regional e conexões Ethernet terrestres para um dos locais com suporte do provedor Exchange. Em seguida, você pode emparelhar com a Microsoft no local do provedor. Verifique a última seção de [Locais e Parceiros da Rota Expressa](expressroute-locations.md) para ver se o seu provedor de serviço está presente em qualquer um dos locais do Exchange. Em seguida, você pode solicitar um circuito de Rota Expressa por meio do provedor de serviço para se conectar ao Azure.

### <a name="how-much-does-expressroute-cost"></a>Quanto custa a Rota Expressa?
Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações a respeito.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Se eu pago por um circuito de Rota Expressa de uma determinada largura de banda, a conexão VPN que adquiro do meu provedor de serviços de rede precisa ser a mesma velocidade?
Não. Você pode comprar uma conexão VPN de qualquer velocidade de seu provedor de serviços. No entanto, sua conexão com o Azure será limitada à largura de banda do circuito de Rota Expressa que você comprar.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-required"></a>Se eu pago por um circuito de Rota Expressa de uma determinada largura de banda, tenho a capacidade de chegar até maiores velocidades, se necessário?
Sim. Circuitos de Rota Expressa são configurados para dar suporte a casos em que você pode disparar até duas vezes o limite de largura de banda que você adquiriu, sem nenhum custo adicional. Verifique com seu provedor de serviços se eles dão suporte a essa funcionalidade.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Posso usar a mesma conexão de rede privada com Rede Virtual e outros serviços do Azure simultaneamente?
Sim. Um circuito de ExpressRoute, uma vez instalado, permitirá que você acesse os serviços em uma rede virtual e outros serviços do Azure simultaneamente. Você se conectará às redes virtuais pelo caminho privado de emparelhamento e a outros serviços pelo caminho público de emparelhamento.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>A Rota Expressa oferece um SLA (contrato de nível de serviço)?
Consulte a [página SLA de Rota Expressa](https://azure.microsoft.com/support/legal/sla/) para obter mais informações.

## <a name="supported-services"></a>Serviços com suporte
O ExpressRoute dá suporte a [três domínios de roteamento](expressroute-circuit-peerings.md) para vários tipos de serviço.

Emparelhamento privado
* Redes virtuais, incluindo todas as máquinas virtuais e serviços de nuvem

Emparelhamento público
* A maioria dos serviços do Azure, com algumas exceções abaixo
* Power BI
* Dynamics 365 for Operations (anteriormente conhecido como Dynamics AX Online)

Emparelhamento da Microsoft
* [Office 365](http://aka.ms/ExpressRouteOffice365)
* A maioria dos serviços do Dynamics 365 (anteriormente conhecido como CRM Online)
  * Dynamics 365 for Sales
  * Dynamics 365 for Customer Service
  * Dynamics 365 for Field Service
  * Dynamics 365 for Project Service

Não há suporte para os seguintes serviços do Azure no ExpressRoute
* CDN
* Teste de carga do Visual Studio Team Services
* Multi-factor Authentication
* Gerenciador de Tráfego

## <a name="data-and-connections"></a>Dados e conexões
### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Há limites para a quantidade de dados que posso transferir usando Rota Expressa?
Não podemos definir um limite para a quantidade de transferência de dados. Consulte os [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para obter informações sobre as taxas de largura de banda.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Quais velocidades de conexão têm suporte pela Rota Expressa?
Ofertas de largura de banda com suporte:

|50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps|

### <a name="which-service-providers-are-available"></a>Que provedores de serviços estão disponíveis?
Consulte [Locais e Parceiros da Rota Expressa](expressroute-locations.md) para a lista de locais e provedores de serviços.

## <a name="technical-details"></a>Detalhes técnicos
### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Quais são os requisitos técnicos para conectar meu espaço local ao Azure?
Consulte a [Página de pré-requisitos para Rota Expressa](expressroute-prerequisites.md) para requisitos.

### <a name="are-connections-to-expressroute-redundant"></a>As conexões à Rota Expressa são redundantes?
Sim. Cada circuito da Rota Expressa tem um par redundante de conexões cruzadas configurado para fornecer alta disponibilidade.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Eu perderei a conectividade se um dos meus links de Rota Expressa falhar?
Você não perderá conectividade se uma das conexões cruzadas falhar. Uma conexão redundante estará disponível para dar suporte à carga de sua rede. Além disso, você pode criar vários circuitos em um local de emparelhamento diferente para obter resiliência a falhas.

### <a name="a-nameonep2plinkaif-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Se eu não estiver colocalizado em uma troca de nuvem e meu provedor de serviços oferecer conexão ponto a ponto, preciso solicitar duas conexões físicas entre minha rede local e a Microsoft?
Não, você só precisará de uma conexão física se seu provedor de serviços puder estabelecer dois circuitos virtuais de Ethernet por meio da conexão física. A conexão física (por exemplo, uma fibra óptica) será encerrada em um dispositivo de camada 1 (L1) (confira a imagem abaixo). Os dois circuitos virtuais de Ethernet estão marcados com IDs de VLAN diferentes, uma para o circuito primário e outra para o secundário. As IDs de VLAN estão no cabeçalho de Ethernet 802.1Q exterior. O cabeçalho de Ethernet 802.1Q interior (não mostrado) é mapeado para um [domínio de roteamento de Rota Expressa](expressroute-circuit-peerings.md)específico. 

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Posso estender uma das minhas VLANs ao Azure usando a Rota Expressa?
Não. Não há suporte para extensões de conectividade de camada 2 ao Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Posso ter mais de um circuito de Rota Expressa em minha assinatura?
Sim. Você pode ter mais de um circuito de Rota Expressa em sua assinatura. O limite padrão para o número de circuitos dedicados está definido como 10. Você pode contatar Suporte da Microsoft para aumentar o limite, se necessário.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Posso ter circuitos de Rota Expressa de diferentes provedores de serviços?
Sim. Você pode ter circuitos de Rota Expressa de muitos provedores de serviços. Cada circuito de Rota Expressa será associado apenas a um provedor de serviços.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Como conectar minhas redes virtuais a um circuito da Rota Expressa
As etapas básicas são descritas a seguir.

* Você deve estabelecer um circuito de Rota Expressa e fazer com que o provedor de serviços o habilite.
* Você ou o provedor deve configurar os emparelhamentos BGP.
* É necessário vincular uma Rede Virtual ao circuito de Rota Expressa.

Consulte [Fluxos de trabalho da Rota Expressa para provisionamento e estados do circuito](expressroute-workflows.md) para saber mais.

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existem limites de conectividade para meu circuito de Rota Expressa?
Sim. [Locais e Parceiros da Rota Expressa](expressroute-locations.md) fornece uma visão geral dos limites de conectividade para um circuito de Rota Expressa. A conectividade de um circuito de Rota Expressa é limitada a uma única região geopolítica. A conectividade pode ser expandida para várias regiões geopolíticas habilitando o recurso premium da Rota Expressa.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Posso vincular mais de uma Rede Virtual a um circuito de Rota Expressa?
Sim. Você pode vincular até 10 redes virtuais a um circuito de Rota Expressa.

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Tenho várias assinaturas do Azure que contêm redes virtuais. Posso conectar redes virtuais pertencentes a assinaturas separadas a um circuito da Rota Expressa?
Sim. Você pode autorizar até 10 outras assinaturas do Azure para usar um único circuito de Rota Expressa. Esse limite pode ser aumentado habilitando o recurso premium da Rota Expressa.

Para obter mais detalhes, consulte [Compartilhando um circuito de Rota Expressa entre várias assinaturas](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>As redes virtuais estão conectadas ao mesmo circuito e isoladas umas das outras?
Não. Todas as redes virtuais vinculadas ao mesmo circuito de Rota Expressa fazem parte do mesmo domínio de roteamento e não estão isoladas entre si, segundo uma perspectiva de roteamento. Se você precisar de isolamento de rota, você precisará criar um circuito de Rota Expressa separado.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Posso conectar uma Rede Virtual a mais de um circuito de Rota Expressa?
Sim. Você pode vincular uma única rede virtual a até 4 circuitos de Rota Expressa. Eles devem ser ordenados por meio de quatro [locais diferentes de Rota Expressa](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Poderei acessar a Internet por meio de minhas redes virtuais conectadas a circuitos de Rota Expressa?
Sim. Se você não anunciou rotas padrão (0.0.0.0/0) ou prefixos de rotas de Internet ao longo da sessão BGP, você será capaz de se conectar à Internet por meio de uma rede virtual vinculada a um circuito de Rota Expressa.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Posso bloquear a conectividade à Internet em minhas redes virtuais conectadas a circuitos de Rota Expressa?
Sim. Você pode anunciar rotas padrão (0.0.0.0/0) para bloquear toda a conectividade com a Internet para máquinas virtuais implantadas em uma rede virtual e rotear todo o tráfego de saída através do circuito de Rota Expressa. Observe que se você anunciar rotas padrão, forçaremos o tráfego para serviços oferecidos por emparelhamento público (como o armazenamento do Azure e banco de dados SQL) de volta para o seu local. Você precisará configurar seus roteadores para retornar o tráfego para o Azure através do caminho de emparelhamento público ou pela Internet.

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>As redes virtuais vinculadas ao mesmo circuito de Rota Expressa podem conversar entre si?
Sim. Máquinas virtuais implantadas em redes virtuais conectadas ao mesmo circuito de Rota Expressa podem comunicar-se umas com as outras.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Posso usar a conectividade site a site para redes virtuais em conjunto com a Rota Expressa?
Sim. A Rota Expressa pode coexistir com VPN dos tipos site a site.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>Posso alterar uma rede virtual usando configuração site a site / ponto a site para que ela passe a usar Rota Expressa?
Sim. Você terá que criar um gateway de Rota Expressa em sua rede virtual. Haverá um pequeno tempo de inatividade associado ao processo.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>O que é necessário para conexão ao armazenamento do Azure por meio de Rota Expressa?
Você deve estabelecer um circuito de Rota Expressa e configurar rotas para emparelhamento público.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Há limites no número de rotas que posso anunciar?
Sim. Aceitamos até 4.000 prefixos de rota para emparelhamento privado e 200 para cada emparelhamento público e da Microsoft. Você poderá aumentar esse limite para 10.000 rotas para emparelhamento privado se habilitar o recurso premium da Rota Expressa.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Há restrições de intervalos de endereços IP que posso anunciar durante a sessão BGP?
Não aceitamos prefixos privados (RFC1918) na sessão BGP de emparelhamento público e da Microsoft.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>O que acontece se eu exceder os limites de BGP?
As sessões BGP serão interrompidas. Elas serão redefinidas quando a contagem de prefixos voltar a ficar abaixo do limite.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>O que é o tempo de espera de BGP da Rota Expressa? Pode ser ajustado?
O tempo de espera é de 180. As mensagens keep-alive são enviadas a cada 60 segundos. Essas são configurações fixas no lado do Microsoft que não podem ser alteradas.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Após anunciar a rota padrão (0.0.0.0/0) para minhas redes virtuais, eu não posso ativar o Windows sendo executado em minhas VMs do Azure. Como posso corrigir isso?
As etapas a seguir ajudarão o Azure a reconhecer a solicitação de ativação:

1. Estabeleça o emparelhamento público para seu circuito de Rota Expressa.
2. Execute uma pesquisa DNS e localize o endereço IP de **kms.core.windows.net**
3. Então, execute um dos dois itens a seguir para que o Serviço de Gerenciamento de Chaves reconheça que a solicitação de ativação é proveniente do Azure e atenda a solicitação.
   * Em sua rede local, faça o roteamento do tráfego destinado ao endereço IP (obtido na etapa 2) de volta para o Azure por meio de emparelhamento público.
   * Faça com que seu provedor NSP envie o tráfego por loop “hairpin” de volta ao Azure, via emparelhamento público.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Posso alterar a largura de banda de um circuito de Rota Expressa?
Sim. Você pode aumentar a largura de banda de um circuito de Rota Expressa sem precisar subdividi-lo. Você terá de fazer o acompanhamento junto ao seu provedor de conectividade para garantir que eles atualizem os gargalos em suas redes, para dar suporte ao aumento de largura de banda. Você não poderá, todavia, reduzir a largura de banda de um circuito de Rota Expressa. Precisar reduzir a largura de banda significa uma desmontagem e recriação de um circuito de Rota Expressa.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Como posso alterar a largura de banda de um circuito de Rota Expressa?
Você pode atualizar a largura de banda do circuito de Rota Expressa usando o cmdlet do PowerShell e API de circuito dedicado de atualização.

## <a name="expressroute-premium"></a>Rota Expressa Premium
### <a name="what-is-expressroute-premium"></a>O que é a Rota Expressa Premium?
Rota Expressa premium é uma coleção de recursos listados abaixo.

* Limite maior na tabela de roteamento, de 4000 rotas para 10.000 rotas, para emparelhamento privado.
* Maior número de VNets que podem ser conectadas ao circuito de Rota Expressa (o padrão é 10). Consulte a tabela seguir para obter mais detalhes.
* Conectividade global através da rede de núcleo da Microsoft. Agora, você poderá conectar uma VNet em uma região geopolítica a um circuito de Rota Expressa em outra região. **Exemplo:** é possível conectar uma VNet criada na Europa Ocidental a um circuito de Rota Expressa criado no Vale do Silício.
* Conectividade com serviços do Office 365 e CRM Online.

### <a name="how-many-vnets-can-i-link-to-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a>Quantas VNets posso vincular a um circuito de Rota Expressa se eu habilitei a Rota Expressa premium?
As tabelas a seguir mostram os limites da Rota Expressa e o número de redes virtuais por circuito de Rota Expressa.

[!INCLUDE [expressroute-limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Como habilito a Rota Expressa premium?
Os recursos da Rota Expressa premium podem ser ativados quando o recurso está habilitado, e podem ser desativados atualizando o estado do circuito. Você pode habilitar a Rota Expressa premium no momento da criação de circuito ou pode chamar o cmdlet do PowerShell/API do circuito dedicado de atualização para habilitar a Rota Expressa premium.

### <a name="how-do-i-disable-expressroute-premium"></a>Como desabilito a Rota Expressa premium?
Você pode desabilitar o ExpressRoute Premium chamando a API do circuito dedicado de atualização / cmdlet do PowerShell. Certifique-se de que tenha dimensionado suas necessidades de conectividade para atender os limites padrão antes de desabilitar o ExpressRoute Premium. Haverá falha na solicitação para desabilitar a Rota Expressa premium se sua utilização for expandida além dos limites padrão.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Posso escolher os recursos que desejo do conjunto de recursos premium?
Não. Você não poderá selecionar os recursos de que precisa. Habilitamos todos os recursos quando você ativa a Rota Expressa premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Quanto custa a Rota Expressa premium?
Consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) para informações sobre custo.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Eu pago algo pela Rota Expressa premium além dos encargos padrão da Rota Expressa?
Sim. Os encargos da Rota Expressa premium somam-se aos encargos de circuito de Rota Expressa e aos encargos demandados pelo provedor de conectividade.

## <a name="expressroute-and-office-365-services-and-crm-online"></a>Rota Expressa e serviços do Office 365 e CRM Online
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-crm-online"></a>Como criar um circuito de Rota Expressa para conectar aos serviços do Office 365 e CRM Online?
1. Examine a [página de pré-requisitos do ExpressRoute](expressroute-prerequisites.md) para se certificar de que você atende aos requisitos.
2. Examine a lista de provedores de serviços e locais em [Locais e parceiros da Rota Expressa](expressroute-locations.md) para garantir que suas necessidades de conectividade são atendidas.
3. Planeje seus requisitos de capacidade, revisando [Planejamento de rede e ajuste de desempenho para o Office 365](http://aka.ms/tune/)
4. Siga as etapas relacionadas nos fluxos de trabalho abaixo para configurar a conectividade [Fluxos de trabalho de ExpressRoute para provisionamento do circuito e estados do circuito](expressroute-workflows.md).

> [!IMPORTANT]
> Certifique-se de ter habilitado o complemento premium da Rota Expressa ao configurar a conectividade com os serviços do Office 365 e CRM Online.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-crm-online"></a>Eu preciso habilitar o emparelhamento público do Azure para me conectar aos serviços do Office 365 e CRM Online?
Não, você precisa habilitar somente o Emparelhamento da Microsoft. O tráfego de autenticação no Azure AD será enviado pelo emparelhamento da Microsoft. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-crm-online"></a>Meus circuitos da Rota Expressa existentes dão suporte a conectividade com os serviços do Office 365 e CRM Online?
Sim. Seus circuitos da Rota Expressa existentes podem ser configurados para dar suporte a conectividade com os serviços do Office 365. Verifique se você tem capacidade suficiente para se conectar aos serviços do Office 365 e certifique-se de que você habilitou o complemento premium. [Planejamento da rede e ajuste de desempenho para o Office 365](http://aka.ms/tune/) ajudarão você a planejar suas necessidades de conectividade. Veja também [Criar e modificar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Quais serviços do Office 365 podem ser acessados por uma conexão de Rota Expressa?
Confira a página [Intervalos de endereço IP e URLs do Office 365](http://aka.ms/o365endpoints) para obter uma lista atualizada dos serviços com suporte do ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-and-crm-online-cost"></a>Qual é o custo da Rota Expressa para serviços do Office 365 e CRM Online?
Os serviços do Office 365 e CRM Online requerem o complemento premium para serem habilitados. A [página de detalhes de preços](https://azure.microsoft.com/pricing/details/expressroute/) fornece detalhes de custos para a Rota Expressa.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Em que regiões há suporte para Rota Expressa para Office 365?
Consulte [Locais e parceiros da Rota Expressa](expressroute-locations.md) para obter mais informações sobre a lista de parceiros e os locais onde há suporte para a Rota Expressa.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Posso acessar o Office 365 pela Internet mesmo se a Rota Expressa foi configurada para minha organização?
Sim. Pontos de extremidade do serviço do Office 365 estão acessíveis pela Internet, embora a Rota Expressa tenha sido configurada para sua rede. Se você estiver em um local configurado para se conectar a serviços do Office 365 por meio de Rota Expressa, você se conectará por meio de Rota Expressa.

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>O Dynamics 365 for Operations (anteriormente conhecido como Dynamics AX Online) pode ser acessado por uma conexão do ExpressRoute?
Sim. O [Dynamics 365 for Operations](https://www.microsoft.com/dynamics365/operations) é hospedado no Azure. Você pode habilitar o emparelhamento público do Azure no circuito do ExpressRoute para se conectar a ele. 




<!--HONumber=Feb17_HO4-->


