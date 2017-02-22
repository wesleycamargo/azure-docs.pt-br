---
title: "Configurar conexões VPN de Site a Site e de ExpressRoute que possam coexistir para o modelo de implantação do Gerenciador de Recursos | Microsoft Docs"
description: "Este artigo o orienta na configuração da conexão VPN de Site a Site e de Rota Expressa que pode coexistir para o modelo do Gerenciador de Recursos."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 68ad5679a2a6fcb7cfca25410af3f2f113490bdc


---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-for-the-resource-manager-deployment-model"></a>Configurar as conexões coexistentes Site a Site e de Rota Expressa para o modelo de implantação do Gerenciador de Recursos
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - clássico](expressroute-howto-coexist-classic.md)
> 
> 

Poder configurar a VPN site a site e a Rota Expressa tem várias vantagens. Você pode configurar a VPN Site a Site como um caminho de failover seguro para a Rota Expressa ou usar VPNs Site a Site para se conectar a sites que não estão conectados por meio da Rota Expressa. Neste artigo, analisaremos as etapas para configurar as duas situações. Este artigo se aplica ao modelo de implantação do Gerenciador de Recursos. Essa configuração não está disponível no portal do Azure.

**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Os circuitos de Rota Expressa devem ser previamente configurados antes de você seguir as instruções abaixo. Verifique se você seguiu as guias para [criar um circuito de ExpressRoute](expressroute-howto-circuit-arm.md) e [configurar o roteamento](expressroute-howto-routing-arm.md) antes de seguir as etapas abaixo.
> 
> 

## <a name="limits-and-limitations"></a>Limites e limitações
* **Não há suporte para o roteamento do tráfego.** Não é possível fazer o roteamento (por meio do Azure) entre sua rede local conectada via VPN Site a Site e sua rede local conectada via ExpressRoute.
* **Não há suporte para o gateway SKU básico.** Você deve usar um gateway SKU não Básico para o [gateway de ExpressRoute](expressroute-about-virtual-network-gateways.md) e o [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Há suporte para apenas um gateway de VPN baseado em rotas.** Você deve usar uma rota baseada no [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **O roteamento estático deve ser configurado para o gateway de VPN.** Se sua rede local estiver conectada à VPN Site a Site e de ExpressRoute, será necessário ter uma rota estática configurada em sua rede local para rotear a conexão VPN Site a Site para a Internet pública.
* **O gateway de ExpressRoute deve ser configurado primeiro.** Você deve criar o gateway de ExpressRoute primeiro, antes de adicionar o gateway de VPN Site a Site.

## <a name="configuration-designs"></a>Designs de configuração
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurar uma VPN site a site como um caminho de failover para a Rota Expressa
Você pode configurar uma conexão VPN site a site como um backup para a Rota Expressa. Isso se aplica apenas às redes virtuais vinculadas ao caminho de emparelhamento privado do Azure. Não há uma solução de failover com base em VPN para serviços acessíveis por meio de emparelhamentos público do Azure e da Microsoft. O circuito da Rota Expressa sempre será o link principal. Os dados só fluirão pelo caminho da VPN site a site se o circuito da Rota Expressa falhar.

> [!NOTE]
> Embora o circuito ExpressRoute seja preferencial em relação à VPN Site a Site quando ambas as rotas são as mesmas, o Azure usa a correspondência de prefixo mais longa para escolher a rota até o destino do pacote.
> 
> 

![Coexistência](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurar uma VPN site a site para se conectar a sites não conectados por meio da Rota Expressa
Você pode configurar sua rede de modo que alguns sites se conectem diretamente ao Azure através da VPN site a site, e alguns sites se conectem por meio da Rota Expressa. 

![Coexistência](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Não é possível configurar uma rede virtual como um roteador de trânsito.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Selecionando as etapas de uso
Há dois conjuntos de procedimentos diferentes para configurar as conexões de modo que elas coexistam. O procedimento de configuração selecionado dependerá caso você já tenha uma rede virtual à qual deseja se conectar ou caso queira criar uma nova rede virtual.

* Não tenho uma VNet e preciso criar uma.
  
    Se você ainda não tiver uma rede virtual, esse procedimento explicará como criar uma nova rede virtual usando o modelo de implantação do Gerenciador de Recursos e criando novas conexões de VPN Site a Site e de Rota Expressa. Para configurar, siga as etapas na seção do artigo [Para criar uma nova rede virtual e conexões coexistentes](#new).
* Eu já tenho uma VNet do modelo de implantação do Gerenciador de Recursos.
  
    Talvez você já tenha uma rede virtual implementada com uma conexão de VPN Site a Site ou uma conexão de Rota Expressa existente. A seção [Para configurar conexões coexistentes para uma VNet já existente](#add) explica como excluir o gateway, então, criar novas conexões de VPN Site a Site e de Rota Expressa. Observe que, ao criar novas conexões, as etapas devem ser concluídas em uma ordem muito específica. Não use as instruções de outros artigos para criar seus gateways e conexões.
  
    Neste procedimento, a criação de conexões que possam coexistir exigirá que você exclua seu gateway e então configure novos gateways. Isso significa que haverá tempo de inatividade nas suas conexões entre locais durante o processo de exclusão e recriação de seu gateway e conexões, mas você não precisará migrar nenhuma das suas VMs ou serviços para uma nova rede virtual. Suas VMs e serviços ainda poderão se comunicar por meio do balanceador de carga enquanto você configura o seu gateway, se estiverem configurados para fazer isso.

## <a name="a-namenewato-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Para criar uma nova rede virtual e conexões coexistentes
Este procedimento orientará você na criação de uma Rede Virtual, bem como na criação das conexões site a site e de Rota Expressa que coexistirão.

1. Você precisará instalar a versão mais recente dos cmdlets do Azure PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para essa configuração podem ser ligeiramente diferentes daqueles com os quais você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções.
2. Entre em sua conta e configure o ambiente.
   
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
3. Crie uma rede virtual incluindo uma Sub-rede de Gateway. Para saber mais sobre a configuração da rede virtual, consulte [Configuração da Rede Virtual do Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).
   
   > [!IMPORTANT]
   > A Sub-rede de Gateway deve ser /27 ou um prefixo mais curto (como /26 ou /25).
   > 
   > 
   
    Crie uma nova VNet.
   
        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 
   
    Adicione sub-redes.
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    Salve a configuração da VNet.
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
4. <a name="gw"></a>Crie um gateway de Rota Expressa. Para obter mais informações sobre a configuração do gateway de Rota Expressa, confira [Configuração do gateway de Rota Expressa](expressroute-howto-add-gateway-resource-manager.md). O Gateway SKU deve ser *Standard*, *HighPerformance* ou *UltraPerformance*.
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 
5. Vincule o gateway de Rota Expressa ao circuito de Rota Expressa. Após essa etapa for concluída, a conexão entre sua rede local e o Azure, por meio de Rota Expressa, é estabelecida. Para obter mais informações sobre a operação de vinculação, confira [Vincular VNets à Rota Expressa](expressroute-howto-linkvnet-arm.md).
   
        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
6. <a name="vpngw"></a>Em seguida, crie seu gateway de VPN Site a Site. Para obter mais informações sobre a configuração do gateway de VPN, consulte [Configurar uma VNet com uma conexão Site a Site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). O Gateway SKU deve ser *Standard*, *HighPerformance* ou *UltraPerformance*. O VpnType deve ser *RouteBased*.
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"
   
    O gateway de VPN do Azure suporta o BGP. Você pode especificar - EnableBgp no comando a seguir.
   
        $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -EnableBgp $true
   
    Você pode encontrar o IP do emparelhamento BGP e o número AS que o Azure usa para o gateway de VPN em $azureVpn.BgpSettings.BgpPeeringAddress e $azureVpn.BgpSettings.Asn. Para obter mais informações, consulte [Configurar BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) para o gateway de VPN do Azure.
7. Crie uma entidade de gateway de VPN de site local. Esse comando não configura seu gateway de VPN local. Em vez disso, ele permite que você forneça as configurações de gateway local, como o IP público e o espaço para endereço local, para que o gateway de VPN do Azure possa se conectar a ele.
   
    Se seu dispositivo VPN local só dá suporte ao roteamento estático, você pode configurar as rotas estáticas da seguinte maneira.
   
        $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   
    Se seu dispositivo VPN local suporta o BGP e você deseja habilitar o roteamento dinâmico, é preciso saber o IP do emparelhamento BGP e o número AS que seu dispositivo VPN local usa.
   
        $localVPNPublicIP = "<Public IP>"
        $localBGPPeeringIP = "<Private IP for the BGP session>"
        $localBGPASN = "<ASN>"
        $localAddressPrefix = $localBGPPeeringIP + "/32"
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
8. Configure seu dispositivo VPN local para conectar o novo gateway de VPN do Azure. Para obter mais informações sobre a configuração de dispositivo VPN, consulte [Configuração do Dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
9. Vincule o gateway de VPN Site a Site no Azure ao gateway local.
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>

## <a name="a-nameaddato-configure-coexsiting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Para configurar conexões coexistentes para uma VNet já existente
Se você tiver uma rede virtual existente, verifique o tamanho da sub-rede do gateway. Se a sub-rede do gateway é /28 ou /29, você deve primeiro excluir o gateway da rede virtual e aumentar o tamanho de sub-rede do gateway. As etapas nesta seção mostram como fazer isso.

Se a sub-rede do gateway é /27 ou maior e a rede virtual está conectada via Rota Expressa, você pode ignorar as etapas abaixo e ir para a ["Etapa 6: criar um gateway de VPN Site a Site"](#vpngw) na seção anterior. 

> [!NOTE]
> Quando você exclui o gateway existente, suas instalações locais perdem a conexão à sua rede virtual enquanto você está trabalhando nessa configuração. 
> 
> 

1. Você precisará instalar a versão mais recente dos cmdlets do Azure PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para essa configuração podem ser ligeiramente diferentes daqueles com os quais você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções. 
2. Exclua o gateway de Rota Expressa ou gateway de VPN Site a Site existente. 
   
        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
3. Exclua a Sub-rede de Gateway.
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
4. Adicione uma Sub-rede de Gateway que seja /27 ou maior.
   
   > [!NOTE]
   > Se você não tiver endereços IP suficientes restantes em sua rede virtual para aumentar o tamanho da sub-rede do gateway, precisará adicionar mais espaço de endereço IP.
   > 
   > 
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    Salve a configuração da VNet.
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
5. Neste ponto, você terá uma VNet sem nenhum gateway. Para criar novos gateways e concluir suas conexões, você pode prosseguir com a [Etapa 4: criar um gateway de Rota Expressa](#gw), encontrada no conjunto de etapas anterior.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Para adicionar a configuração de ponto a site ao gateway de VPN
Você pode seguir as etapas abaixo para adicionar a configuração de Ponto a Site ao gateway de VPN em uma configuração de coexistência.

1. Adicione o pool de endereços do Cliente VPN. 
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
2. Carregue o certificado-raiz da VPN para Azure para seu gateway de VPN. Neste exemplo, presume-se que o certificado raiz está armazenado no computador local onde os seguintes cmdlets do PowerShell são executados. 
   
        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Para saber mais sobre a VPN de Ponto a Site, confira [Configurar uma conexão de Ponto a Site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).




<!--HONumber=Dec16_HO2-->


