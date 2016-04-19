<properties
   pageTitle="Configurar conexões VPN de Site a Site e de Rota Expressa que possam coexistir para o modelo de implantação do Gerenciador de Recursos | Microsoft Azure"
   description="Este artigo o orienta na configuração da conexão VPN de Site a Site e de Rota Expressa que pode coexistir para o modelo do Gerenciador de Recursos."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/06/2016"
   ms.author="charleywen"/>

# Configurar as conexões coexistentes Site a Site e de Rota Expressa para o modelo de implantação do Gerenciador de Recursos

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - clássico](expressroute-howto-coexist-classic.md)

Poder configurar a VPN site a site e a Rota Expressa tem várias vantagens. Você pode configurar a VPN Site a Site como um caminho de failover seguro para a Rota Expressa ou usar VPNs Site a Site para se conectar a sites que não estão conectados por meio da Rota Expressa. Neste artigo, analisaremos as etapas para configurar as duas situações. Este artigo se aplica ao modelo de implantação do Gerenciador de Recursos. Essa configuração não está disponível no portal do Azure.


**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

>[AZURE.IMPORTANT] Os circuitos de Rota Expressa devem ser previamente configurados antes de você seguir as instruções abaixo. É necessário que você tenha seguido os guias para [criar um circuito da Rota Expressa](expressroute-howto-circuit-arm.md) e [configurar o roteamento](expressroute-howto-routing-arm.md) antes de seguir as etapas abaixo.

## Limites e limitações

- **O roteamento em trânsito não tem suporte:** não é possível fazer o roteamento (via Azure) entre sua rede local conectada via VPN Site a Site e sua rede local conectada via Rota Expressa.
- **O túnel forçado não pode ser habilitado no gateway de VPN Site a Site:** você só pode “forçar” todo o tráfego direcionado para a Internet de volta para sua rede local por meio da Rota Expressa. 
- **Somente gateways padrão ou de alto desempenho:** você deve usar um gateway padrão ou de alto desempenho para o gateway da Rota Expressa e o gateway de VPN Site a Site. Confira [SKUs de gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) para obter informações sobre SKUs de gateway.
- **Somente gateway de VPN baseado em rota:** você deve usar um gateway de VPN baseado em rota. Consulte [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) para obter informações sobre o gateway de VPN baseado em rota.
- **Requisito de rota estática:** se sua rede local estiver conectada à VPN Site a Site e a uma Rota Expressa, será necessário ter uma rota estática configurada em sua rede local para rotear a conexão VPN Site a Site para a Internet pública.
- **O gateway de Rota Expressa deve ser configurado primeiro:** você deve criar o gateway de Rota Expressa primeiro, antes de adicionar o gateway de VPN Site a Site.


## Designs de configuração

### Configurar uma VPN site a site como um caminho de failover para a Rota Expressa

Você pode configurar uma conexão VPN site a site como um backup para a Rota Expressa. Isso se aplica apenas às redes virtuais vinculadas ao caminho de emparelhamento privado do Azure. Não há uma solução de failover com base em VPN para serviços acessíveis por meio de emparelhamentos público do Azure e da Microsoft. O circuito da Rota Expressa sempre será o link principal. Os dados só fluirão pelo caminho da VPN site a site se o circuito da Rota Expressa falhar.

![Coexistência](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### Configurar uma VPN site a site para se conectar a sites não conectados por meio da Rota Expressa

Você pode configurar sua rede de modo que alguns sites se conectem diretamente ao Azure através da VPN site a site, e alguns sites se conectem por meio da Rota Expressa.

![Coexistência](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] Não é possível configurar uma rede virtual como um roteador de trânsito.

## Selecionando as etapas de uso

Há dois conjuntos de procedimentos diferentes para configurar as conexões de modo que elas coexistam. O procedimento de configuração selecionado dependerá caso você já tenha uma rede virtual à qual deseja se conectar ou caso queira criar uma nova rede virtual.


- Não tenho uma VNet e preciso criar uma.
	
	Se você ainda não tiver uma rede virtual, esse procedimento explicará como criar uma nova rede virtual usando o modelo de implantação do Gerenciador de Recursos e criando novas conexões de VPN Site a Site e de Rota Expressa. Para configurar, siga as etapas na seção do artigo [Para criar uma nova rede virtual e conexões coexistentes](#new).

- Eu já tenho uma VNet do modelo de implantação do Gerenciador de Recursos.

	Talvez você já tenha uma rede virtual implementada com uma conexão de VPN Site a Site ou uma conexão de Rota Expressa existente. A seção [Para configurar conexões coexistentes para uma VNet já existente](#add) explica como excluir o gateway, então, criar novas conexões de VPN Site a Site e de Rota Expressa. Observe que, ao criar novas conexões, as etapas devem ser concluídas em uma ordem muito específica. Não use as instruções de outros artigos para criar seus gateways e conexões.

	Neste procedimento, a criação de conexões que possam coexistir exigirá que você exclua seu gateway e então configure novos gateways. Isso significa que haverá tempo de inatividade nas suas conexões entre locais durante o processo de exclusão e recriação de seu gateway e conexões, mas você não precisará migrar nenhuma das suas VMs ou serviços para uma nova rede virtual. Suas VMs e serviços ainda poderão se comunicar por meio do balanceador de carga enquanto você configura o seu gateway, se estiverem configurados para fazer isso.


## <a name="new"></a>Para criar uma nova rede virtual e conexões coexistentes

Este procedimento orientará você na criação de uma Rede Virtual, bem como na criação das conexões site a site e de Rota Expressa que coexistirão.
	
1. Você precisará instalar a versão mais recente dos cmdlets do Azure PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para essa configuração podem ser ligeiramente diferentes daqueles com os quais você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções.

2. Entre em sua conta e configure o ambiente.
	
		login-AzureRmAccount
		Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
		$location = "Central US"
		$resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Crie uma rede virtual incluindo uma Sub-rede de Gateway. Para saber mais sobre a configuração da rede virtual, consulte [Configuração da Rede Virtual do Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).

	>[AZURE.IMPORTANT] A Sub-rede de Gateway deve ser /27 ou um prefixo mais curto (como /26 ou /25).
	
	Crie uma nova VNet.

		$vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

	Adicione sub-redes.

		Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	Salve a configuração da VNet.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Crie um gateway de Rota Expressa. Para obter mais informações sobre a configuração do gateway de Rota Expressa, confira [Configuração do gateway de Rota Expressa](expressroute-howto-add-gateway-resource-manager.md). O GatewaySKU deve ser *Standard* ou de *Alto Desempenho*.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		$gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Vincule o gateway de Rota Expressa ao circuito de Rota Expressa. Após essa etapa for concluída, a conexão entre sua rede local e o Azure, por meio de Rota Expressa, é estabelecida. Para obter mais informações sobre a operação de vinculação, confira [Vincular VNets à Rota Expressa](expressroute-howto-linkvnet-arm.md).

		$ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
		New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. Em seguida, crie seu gateway de VPN Site a Site. Para obter mais informações sobre a configuração do gateway de VPN, consulte [Configurar uma conexão entre VNets](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md). O GatewaySKU deve ser *Standard* ou de *Alto Desempenho*. O VpnType deve ser *RouteBased*.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

7. Crie uma entidade de gateway de VPN de site local. Esse comando não configura seu gateway de VPN local. Em vez disso, ele permite que você forneça as configurações de gateway local, como o IP público e o espaço para endereço local, para que o gateway de VPN do Azure possa se conectar a ele.
	>[AZURE.NOTE] Se sua rede local tiver várias rotas, você poderá passá-las como uma matriz. $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")

		$localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix '10.100.0.0/16'

8. Configure seu dispositivo VPN local para conectar o novo gateway de VPN do Azure. Para obter mais informações sobre a configuração de dispositivo VPN, consulte [Configuração do Dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Vincule o gateway de VPN Site a Site no Azure ao gateway local.

		$azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
		New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Para configurar conexões coexistentes para uma VNet já existente

Se você tiver uma rede virtual existente conectada via conexão VPN de Rota Expressa ou Site a Site, será necessário primeiro excluir o gateway existente para que as conexões possam conectar-se à rede virtual existente. Isso significa que suas instalações locais perderão a conexão à sua rede virtual por meio do gateway enquanto você estiver trabalhando nessa configuração.

**Antes de começar a configuração:** verifique se você tem um número suficiente de endereços IP restantes em sua rede virtual, para que você pode aumentar o tamanho da sub-rede de gateway. Observe que você precisará excluir o gateway e recriá-lo, mesmo se você tiver endereços IP suficientes. Isso ocorre porque o gateway deve ser recriado para acomodar as conexões coexistentes.

1. Você precisará instalar a versão mais recente dos cmdlets do Azure PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para essa configuração podem ser ligeiramente diferentes daqueles com os quais você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções. 

2. Exclua o gateway de Rota Expressa ou gateway de VPN Site a Site existente.

		Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Exclua a Sub-rede de Gateway.
		
		$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
		Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Adicione uma Sub-rede de Gateway que seja /27 ou maior.

		$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	Salve a configuração da VNet.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. Neste ponto, você terá uma VNet sem nenhum gateway. Para criar novos gateways e concluir suas conexões, você pode prosseguir com a [Etapa 4: criar um gateway de Rota Expressa](#gw), encontrada no conjunto de etapas anterior.

## Para adicionar a configuração de ponto a site ao gateway de VPN
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

Para obter mais informações sobre a VPN de Ponto a Site, consulte [Configurar uma conexão de Ponto a Site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## Próximas etapas

Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).

<!---HONumber=AcomDC_0413_2016-->