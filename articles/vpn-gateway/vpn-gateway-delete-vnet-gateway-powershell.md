---
title: 'Excluir um gateway de rede virtual: PowerShell: Azure Resource Manager | Microsoft Docs'
description: "Exclua um gateway de rede virtual usando o PowerShell no modelo de implantação do Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 3032b09d06a103f9cd915e3803355199243488f9
ms.lasthandoff: 03/21/2017


---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Excluir um gateway de rede virtual usando o PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clássico - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Há duas abordagens diferentes que podem ser executadas quando você deseja excluir um gateway de rede virtual de uma configuração de Gateway de VPN.

- Se você quiser excluir tudo e começar novamente, por exemplo, no caso de um ambiente de teste, você poderá excluir um grupo de recursos inteiro. Quando você exclui um grupo de recursos, isso exclui todos os recursos dentro do grupo. Isso é recomendado somente se você não deseja manter os recursos no grupo de recursos. Você não pode excluir seletivamente apenas alguns recursos usando essa abordagem.

- Se você quiser manter alguns dos recursos no seu grupo de recursos, a exclusão de um gateway de rede virtual se tornará um pouco mais complicada. Antes de poder excluir o gateway de rede virtual, primeiro você deve excluir todos os recursos que são dependentes do gateway. As etapas a que seguir dependem do tipo de conexões que você criou e os recursos dependentes de cada conexão.

##<a name="before-beginning"></a>Antes de começar

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Baixe a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager.
Baixe e instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações sobre como baixar e instalar os cmdlets do PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="2-connect-to-your-azure-account"></a>2. Conectar-se à sua conta do Azure. 
Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

    Login-AzureRmAccount

Verificar as assinaturas da conta.

    Get-AzureRmSubscription

Se você tiver mais de uma assinatura, especifique a assinatura que deseja usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

##<a name="S2S"></a>Excluir um Gateway de VPN site a site

Para excluir um gateway de rede virtual para uma configuração S2S, exclua cada recurso pertencente ao gateway de rede virtual. Os recursos devem ser excluídos em uma determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser chamados especificamente, enquanto outros valores são um resultado de saída. Podemos usar os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do Grupo de Recursos: RG1<br>
Nome do gateway de rede virtual: GW1<br>

As etapas a seguir se aplicam ao modelo de implantação do Resource Manager.

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o gateway de rede virtual que você deseja excluir.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem alguma conexão.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
    $Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}

###<a name="3-delete-all-connections"></a>3. Exclua todas as conexões.
Talvez seja solicitado que você confirme a exclusão de cada uma das conexões.

    $Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="4-get-the-list-of-the-corresponding-local-network-gateways"></a>4. Obtenha a lista de gateways de rede local correspondentes.

    $LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
    
###<a name="5-delete-the-local-network-gateways"></a>5. Exclua os gateways de rede local.
Talvez seja solicitado que você confirme a exclusão de cada um dos gateways de rede local.

    $LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}

###<a name="6-delete-the-virtual-network-gateway"></a>6. Exclua o gateway de rede virtual.
Talvez seja solicitado que você confirme a exclusão do gateway.

>[!NOTE]
> Se você tiver uma configuração de P2S nessa VNet além da configuração de S2S, a exclusão do gateway de rede virtual desconectará automaticamente todos os clientes de P2S sem aviso.
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="7-get-the-ip-configurations-of-the-virtual-network-gateway"></a>7. Obtenha as configurações de IP do gateway de rede virtual.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="8-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>8. Obter a lista de endereços IP públicos usados para esse gateway de rede virtual 
Se o gateway de rede virtual estava ativo-ativo, você verá dois endereços IP públicos.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="9-delete-the-public-ips"></a>9. Exclua os IPs públicos.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}

###<a name="10-delete-the-gateway-subnet-and-set-the-configuration"></a>10. Exclua a sub-rede do gateway e defina a configuração.

    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="v2v"></a>Excluir um Gateway de VPN de VNet para VNet

Para excluir um gateway de rede virtual para uma configuração V2V, exclua cada recurso pertencente ao gateway de rede virtual. Os recursos devem ser excluídos em uma determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser chamados especificamente, enquanto outros valores são um resultado de saída. Podemos usar os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do Grupo de Recursos: RG1<br>
Nome do gateway de rede virtual: GW1<br>

As etapas a seguir se aplicam ao modelo de implantação do Resource Manager.

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o gateway de rede virtual que você deseja excluir.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Verifique se o gateway de rede virtual tem alguma conexão.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
Pode haver outras conexões com o gateway de rede virtual que fazem parte de um grupo de recursos diferente. Verifique se há conexões adicionais em cada grupo de recursos adicional. Neste exemplo, estamos verificando se há conexões de RG2. Execute isso para cada grupo de recursos que você tem e que pode ter uma conexão para o gateway de rede virtual.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="3-get-the-list-of-connections-in-both-directions"></a>3. Obtenha a lista de conexões em ambas as direções.
Já que essa é uma configuração de VNet para VNet, você precisa da lista de conexões em ambas as direções.

    $ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
Neste exemplo, estamos verificando se há conexões de RG2. Execute isso para cada grupo de recursos que você tem e que pode ter uma conexão para o gateway de rede virtual.
 
    $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="4-delete-all-connections"></a>4. Exclua todas as conexões.
Talvez seja solicitado que você confirme a exclusão de cada uma das conexões.

    $ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
    $ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="5-delete-the-virtual-network-gateway"></a>5. Exclua o gateway de rede virtual.
Talvez seja solicitado que você confirme a exclusão de cada um dos gateways de rede virtual.

>[!NOTE]
> Se você tiver configurações de P2S nas Vnets além da configuração de V2V, a exclusão dos gateways de rede virtual desconectará automaticamente todos os clientes de P2S sem aviso.
>
>

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="6-get-the-ip-configurations-of-the-virtual-network-gateway"></a>6. Obtenha as configurações de IP do gateway de rede virtual.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="7-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>7. Obtenha a lista de endereços IP públicos usados para esse gateway de rede virtual. 
Se o gateway de rede virtual estava ativo-ativo, você verá dois endereços IP públicos.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="8-delete-the-public-ips"></a>8. Exclua os IPs públicos.
Talvez seja solicitado que você confirme a exclusão do IP público.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="9-delete-the-gateway-subnet-and-set-the-configuration"></a>9. Exclua a sub-rede do gateway e defina a configuração.
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="deletep2s"></a>Excluir um gateway de VPN ponto a site

Para excluir um gateway de rede virtual de uma configuração de P2S, é necessário excluir primeiro cada recurso pertencente ao gateway de rede virtual. Os recursos devem ser excluídos em uma determinada ordem devido a dependências. Ao trabalhar com os exemplos abaixo, alguns dos valores devem ser chamados especificamente, enquanto outros valores são um resultado de saída. Podemos usar os seguintes valores específicos nos exemplos para fins de demonstração:

Nome da VNet: VNet1<br>
Nome do Grupo de Recursos: RG1<br>
Nome do gateway de rede virtual: GW1<br>

As etapas a seguir se aplicam ao modelo de implantação do Resource Manager.


>[!NOTE]
> Quando você excluir o gateway de VPN, todos os clientes conectados serão desconectados da VNet sem aviso.
>
>

###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Obtenha o gateway de rede virtual que você deseja excluir.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-delete-the-virtual-network-gateway"></a>2. Exclua o gateway de rede virtual.
Talvez seja solicitado que você confirme a exclusão de cada um dos gateways de rede virtual.

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="3-get-the-ip-configurations-of-the-virtual-network-gateway"></a>3. Obtenha as configurações de IP do gateway de rede virtual.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="4-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>4. Obtenha a lista de endereços IP públicos usados para esse gateway de rede virtual. 
Se o gateway de rede virtual estava ativo-ativo, você verá dois endereços IP públicos.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="5-delete-the-public-ips"></a>5. Exclua os IPs públicos.
Talvez seja solicitado que você confirme a exclusão do IP público.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="6-delete-the-gateway-subnet-and-set-the-configuration"></a>6. Exclua a sub-rede do gateway e defina a configuração.
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="delete"></a>Excluir um Gateway de VPN por meio da exclusão do grupo de recursos

Se você não estiver preocupado em manter nenhum de seus recursos e apenas desejar recomeçar, você poderá excluir um grupo de recursos inteiro. Essa é uma maneira rápida de remover tudo. Quando você exclui um grupo de recursos inteiro, isso não é seletivo com relação os recursos que você exclui. Portanto, certifique-se de que isso é o que você deseja fazer antes de executar o exemplo.

As etapas a seguir se aplicam ao modelo de implantação do Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Obtenha uma lista de todos os grupos de recursos em sua assinatura.

    Get-AzureRmResourceGroup
### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Localize o grupo de recursos que você deseja excluir.
Localize o grupo de recursos que você deseja excluir e exiba a lista de recursos nesse grupo de recursos. Neste exemplo, o nome do grupo de recursos é RG1. Modificar o exemplo para recuperar uma lista de todos os recursos.

    Find-AzureRmResource -ResourceGroupNameContains RG1

### <a name="3-verify-the-resources-in-the-list"></a>3. Verificar os recursos na lista.
Quando a lista for retornada, examine-a para verificar que você deseja excluir todos os recursos no grupo, bem como o grupo de recursos em si. 

### <a name="4-delete-the-resource-group-and-resources"></a>4. Exclua o grupo de recursos e os recursos.
Para excluir o grupo de recursos e todos os recursos contidos nele, modifique o exemplo e execute-o.

    Remove-AzureRmResourceGroup -Name RG1

### <a name="5-check-the-status"></a>5. Verifique o status.
Leva algum tempo para o Azure excluir todos os recursos. Você pode verificar o status do seu grupo de recursos usando esse cmdlet.

    Get-AzureRmResourceGroup -ResourceGroupName RG1

O resultado retornado mostra 'Êxito'.

    ResourceGroupName : RG1
    Location          : eastus
    ProvisioningState : Succeeded


