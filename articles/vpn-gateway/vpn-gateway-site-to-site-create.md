---
title: "Criar uma rede virtual com uma conexão de Gateway de VPN site a site usando o portal clássico do Azure | Microsoft Docs"
description: "Criar uma Rede Virtual com uma conexão de Gateway de VPN site a site para configurações entre locais e híbridas usando o modelo de implantação clássico."
services: vpn-gateway
documentationcenter: 
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 024ecb29-64de-4ff1-84f1-1a45a8595f0b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc377f80fa6b9df41081b13a51ff15482b2ba8bc


---
# <a name="create-a-vnet-with-a-sitetosite-connection-using-the-azure-classic-portal"></a>Criar uma VNet com uma conexão Site a Site usando o Portal Clássico do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clássico - Portal Clássico](vpn-gateway-site-to-site-create.md)
> 
> 

Este artigo mostra como criar uma rede virtual e uma conexão de gateway de VPN Site a Site com sua rede local usando o modelo de implantação clássica e o portal clássico. As conexões Site a Site podem ser usadas para configurações híbridas e entre instalações.

![Diagrama Site a Site](./media/vpn-gateway-site-to-site-create/site2site.png "site-to-site")

### <a name="deployment-models-and-methods-for-sitetosite-connections"></a>Modelos e métodos de implantação para conexões Site a Site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os modelos de implantação e os métodos de configurações de site a site disponíveis no momento. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais
Se você quiser conectar as Redes Virtuais, veja [Configurar uma conexão de Rede Virtual para Rede Virtual para o modelo de implantação clássico](virtual-networks-configure-vnet-to-vnet-connection.md). Se você deseja adicionar uma conexão Site a Site a uma rede virtual que já tem uma conexão, consulte [Adicionar uma conexão de S2S a uma rede virtual com uma conexão de gateway de VPN existente](vpn-gateway-multi-site.md).

## <a name="before-you-begin"></a>Antes de começar
Verifique se você tem os itens a seguir antes de iniciar a configuração.

* Um dispositivo VPN compatível e alguém que possa configurá-lo. Confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.
* Um endereço IP público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="a-namecreatevnetacreate-your-virtual-network"></a><a name="CreateVNet"></a>Criar sua rede virtual
1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com/).
2. No canto inferior esquerdo da tela, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.
3. Para criar sua rede virtual, insira as configurações nas páginas a seguir:

## <a name="a-namedetailsavirtual-network-details-page"></a><a name="Details"></a>Página Detalhes da Rede Virtual
Insira as seguintes informações:

* **Nome**: nome da sua rede virtual. Por exemplo, *EastUSVNet*. Você usará esse nome de rede virtual quando implantar suas VMs e instâncias de PaaS. Portanto, é melhor não criar um nome muito complicado.
* **Local**: o local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você desejar que as VMs implantadas nesta rede virtual estejam localizadas fisicamente no *leste dos EUA*, selecione esse local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.

## <a name="a-namednsadns-servers-and-vpn-connectivity-page"></a><a name="DNS"></a>Página Servidores DNS e Conectividade VPN
Insira as informações a seguir e, em seguida, clique na seta de avanço no canto inferior direito.

* **Servidores DNS**: insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu de atalho. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes dessa rede virtual.
* **Configurar VPN Site a Site**: marque a caixa de seleção **Configurar uma VPN site a site**.
* **Rede Local**: uma rede local representa sua localização física no local. Você pode selecionar uma rede local já criada anteriormente ou pode criar uma nova rede local. No entanto, se você optar por usar uma rede local criada anteriormente, acesse a página de configuração **Redes Locais** e verifique se o endereço IP do Dispositivo VPN (endereço IPv4 voltado para o público) para o dispositivo VPN seja preciso.

## <a name="a-nameconnectivityasitetosite-connectivity-page"></a><a name="Connectivity"></a>Página Conectividade Site a Site
Se estiver criando uma nova rede local, você verá a página **Conectividade Site a Site** . Se você quiser usar uma rede local criada anteriormente, essa página não será exibida no assistente e você poderá ir para a próxima seção.

Digite as informações a seguir e, em seguida, clique na seta de avanço.

* **Nome**: o nome que você deseja dar ao site de rede local.
* **Endereço IP do Dispositivo VPN**: o endereço IPv4 voltado para o público do dispositivo VPN local que você usa para se conectar ao Azure. O dispositivo VPN não pode ser localizado por trás de um NAT.
* **Espaço de Endereço**: inclua o IP Inicial e a Contagem de Endereços (CIDR). Você especifica o(s) intervalo(s) de endereços que deseja que seja(m) enviado(s) por meio do gateway de rede virtual para o local. Se um endereço IP de destino estiver dentro dos intervalos especificados aqui, ele será roteado por meio do gateway de rede virtual.
* **Adicionar espaço de endereço**: se você tiver vários intervalos de endereço que deseja enviar pelo gateway da rede virtual, especifique cada intervalo de endereço adicional. Você poderá adicionar ou remover intervalos posteriormente na página **Rede Local** .

## <a name="a-nameaddressavirtual-network-address-spaces-page"></a><a name="Address"></a>Página Espaços de endereço de rede virtual
Especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os DIPS (endereços IP dinâmicos) que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual.

É particularmente importante selecionar um intervalo que não se sobreponha a nenhum dos intervalos usados para sua rede local. Você precisa coordenar com o administrador da rede. Talvez seu administrador da rede precise reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.

Insira as informações a seguir e clique na marca de seleção no canto inferior direito para configurar sua rede.

* **Espaço de Endereço**: inclui o IP Inicial e a Contagem de Endereços. Verifique se os espaços de endereço especificados não se sobrepõem a qualquer espaço de endereço em sua rede local.
* **Adicionar sub-rede**: incluindo o IP Inicial e a Contagem de Endereços. Sub-redes adicionais não são necessárias, mas convém criar uma sub-rede separada para VMs que terão DIPS estáticos. Ou então, você pode colocar suas VMs em uma sub-rede separada das outras instâncias de função.
* **Adicionar sub-rede de gateway**: clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada apenas para o gateway de rede virtual e é necessária para esta configuração.

Clique na marca de seleção na parte inferior da página e sua rede virtual começará a ser criada. Quando ela for concluída, você verá **Criada** listada em **Status** na página **Redes** no Portal Clássico do Azure. Depois que a rede virtual tiver sido criada, você poderá configurar seu gateway de rede virtual.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="a-namevnetgatewayaconfigure-your-virtual-network-gateway"></a><a name="VNetGateway"></a>Configurar seu Gateway de Rede Virtual
Configure o gateway de rede virtual para criar uma conexão site a site segura. Consulte [Configurar um gateway da rede virtual no portal clássico do Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Consulte a documentação das [Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/) para saber mais.




<!--HONumber=Nov16_HO2-->


