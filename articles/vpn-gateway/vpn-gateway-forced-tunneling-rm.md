<properties 
   pageTitle="Configurar o túnel forçado para Gateways de VPN usando o Gerenciador de Recursos | Microsoft Azure"
   description="Caso tenha uma Rede Virtual com um Gateway de VPN entre locais, você pode redirecionar ou "forçar" todo o tráfego direcionado à Internet para um local determinado. Este artigo se aplica ao modelo de implantação do Gerenciador de Recursos. "
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/17/2015"
   ms.author="cherylmc" />

# Configurar o túnel forçado usando o PowerShell e Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [PowerShell - Service Management](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)

Este artigo se aplica aos Gateways de Redes Virtuais e de VPN criados com o modelo de implantação do Gerenciador de Recursos do Azure. Caso pretenda configurar o túnel forçado para redes virtuais que foram criadas usando o Gerenciamento de Serviços (também conhecido como modelo de implantação clássico), confira [Configurar o túnel forçado](vpn-gateway-about-forced-tunneling.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Sobre túnel forçado

O túnel forçado permite redirecionar ou "forçar" todo o tráfego direcionado para a Internet de volta para seu local por meio de um túnel VPN de site a site para inspeção e auditoria. Esse é um requisito crítico de segurança para a maioria das políticas de TI empresariais. Sem o túnel forçado, o tráfego direcionado para Internet de suas VMs no Azure sempre percorrerão da infraestrutura de rede do Azure diretamente para a Internet, sem a opção para permitir que você inspecione ou audite o tráfego. O acesso não autorizado à Internet pode levar à divulgação de informações ou outros tipos de violações de segurança.

O diagrama a seguir ilustra o funcionamento do túnel forçado.

![Túnel forçado](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

No exemplo acima, a sub-rede Front-end não é um túnel forçado. As cargas de trabalho na sub-rede do front-end podem continuar a aceitar e a responder diretamente às solicitações de clientes da Internet. As sub-redes de Camada intermediária e Back-end são túneis forçados. As conexões de saída dessas duas sub-redes com a Internet serão forçadas ou redirecionadas de volta ao site local por meio de túneis de VPN S2S. Isso permite que você restrinja e inspecione o acesso à Internet de suas Máquinas Virtuais ou Serviços de Nuvem no Azure, continuando a habilitar a arquitetura de serviço de várias camadas necessária. Você também tem a opção de aplicar o túnel forçado às redes virtuais inteiras se não houver nenhuma carga de trabalho voltada para a Internet em suas redes virtuais.

## Requisitos e considerações

O túnel forçado no Azure é configurado por meio de rotas de rede virtual definidas pelo usuário. Redirecionar o tráfego para um site local é expressado como uma Rota Padrão para o gateway de VPN do Azure. Para saber mais sobre rotas definidas pelo usuário e redes virtuais, confira [Rotas definidas pelo usuário e Encaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).

- Cada sub-rede de rede virtual tem uma tabela de roteamento interna do sistema. A tabela de roteamento do sistema tem estes três grupos de rotas:

	- **Rotas locais de Rede Virtual:** diretamente para as VMs de destino na mesma rede virtual
	
	- **Rotas locais:** para o Gateway de VPN do Azure
	
	- **Rota padrão:** diretamente para a Internet. Observe que os pacotes destinados para os endereços IP privados não cobertos pelas duas rotas anteriores serão removidos.

-  Este procedimento usa rotas definidas pelo usuário (UDR) a fim de criar uma tabela de roteamento para adicionar uma rota padrão e associar a tabela de roteamento às sub-redes de VNet para habilitar o túnel forçado nessas sub-redes.

- O túnel forçado deve ser associado a uma Rede Virtual que tenha um Gateway de VPN de roteamento. Você precisa definir um "site padrão" entre sites locais entre locais conectado à rede virtual.

- Observe que o túnel forçado da Rota Expressa não está configurado por intermédio deste mecanismo, mas é habilitado por meio do anúncio de uma rota padrão por meio de sessões de emparelhamento de BGP da Rota Expressa. Confira a [Documentação da Rota Expressa](https://azure.microsoft.com/documentation/services/expressroute/) para saber mais.

## Configurar o túnel forçado

### Visão geral de configuração

Observe o procedimento a seguir para criar um grupo de recursos e uma Rede Virtual. Em seguida, crie um crie um Gateway de VPN e configure um túnel forçado.

No exemplo, a Rede Virtual "MultiTier-VNet" tem 3 sub-redes: *Front-end*, *Mid-Tier* e *Back-end*, com 4 conexões entre locais: *DefaultSiteHQ* e 3 *Branches*. As etapas do procedimento definem *DefaultSiteHQ* como a conexão de site padrão para o túnel forçado e configuram as sub-redes *Mid-Tier* e *Back-end* para usarem túnel forçado.

	
### Antes de começar

Antes de começar a configurar, verifique se você tem os itens a seguir.

- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

- Cmdlets do Azure PowerShell (1.0 ou posterior) Você pode baixar e instalar essa versão na seção Windows PowerShell da [página Download](http://azure.microsoft.com/downloads/). Esta documentação foi desenvolvida para o PowerShell 1.0 ou posterior. Os cmdlets necessários para esta configuração não estão incluídos nas versões anteriores.

- Caso seja iniciante no Gerenciador de Recursos do Azure e no PowerShell, confira [este artigo](../articles/powershell-azure-resource-manager.md) para saber mais.

### Etapas da configuração

1. No console do PowerShell, entre na conta do Azure. O cmdlet solicita as credenciais de logon dessa conta. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.

		Login-AzureRmAccount 

2. Obtenha uma lista das assinaturas do Azure.

		Get-AzureRmSubscription

2. Especifique a assinatura que você deseja usar.

		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
		
3. Crie um grupos de recursos.

		New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Crie uma rede virtual e especifique as sub-redes.

		$s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
		$s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
		$s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
		$s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
		$vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Crie os gateways de rede local.

		$lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
		$lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
		$lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
		$lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
		
6. Crie uma regra e uma tabela de rotas.

		New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
		$rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
		Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
		Set-AzureRmRouteTable -RouteTable $rt


6. Associe a tabela de rotas criada acima às sub-redes Mid-Tier e Back-end.

		$vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
		Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

9. Crie o gateway com um site padrão. Essa etapa de criação e configuração do gateway leva cerca de 20 minutos ou mais para ser concluída. Apesar de parecer apenas alguns cmdlets no console, há muitos dados em processamento. Observe que o GatewayDefaultSite é o parâmetro do cmdlet que permite o funcionamento da configuração de roteamento forçada, portanto, convém não ignorá-lo. Ele está disponível apenas no PowerShell 1.0 ou posterior.

		$pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
		$gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
		New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

10. Estabelecer conexões VPN site a site

		$gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
		$lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
		$lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
		$lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
		$lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

		Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
		

<!----HONumber=AcomDC_1210_2015-->