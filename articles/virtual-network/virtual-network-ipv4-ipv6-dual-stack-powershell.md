---
title: Implantar um aplicativo de pilha dupla de IPv6 na rede virtual do Azure - PowerShell
titlesuffix: Azure Virtual Network
description: Este artigo mostra como implanta um aplicativo de pilha dupla de IPv6 na rede virtual do Azure usando o Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: f26391e36e3208996160fffad01e39ec2f182318
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130823"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>Implantar um aplicativo de pilha dupla do IPv6 no Azure - PowerShell (versão prévia)

Este artigo mostra como implantar um aplicativo de pilha dual (IPv4 + IPv6) no Azure que inclua uma rede virtual de pilha dupla e uma sub-rede, um balanceador de carga com configurações de front-end dual (IPv4 + IPv6), VMs com NICs que têm uma configuração de IP dual, rede grupo de segurança e IPs públicos.

> [!Important]
> Suporte a IPv6 para a rede Virtual do Azure está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo Azure PowerShell versão 6.9.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="prerequisites"></a>Pré-requisitos
Antes de implantar um aplicativo de pilha dupla no Azure, você deve configurar sua assinatura para esse recurso de visualização usando o Azure PowerShell a seguir:

Registre-se da seguinte maneira:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
Demora até 30 minutos para a conclusão do registro de recursos. Você pode verificar o status do registro, executando o seguinte comando do PowerShell do Azure: Verificar o registro da seguinte maneira:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
Após a conclusão do registro, execute o seguinte comando:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar sua rede virtual de pilha dual, você deve criar um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos denominado *myRGDualStack* na *Leste nos* local:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Criar endereços IP públicos IPv4 e IPv6
Para acessar suas máquinas virtuais da Internet, você precisa de endereços IP públicos IPv4 e IPv6 para o balanceador de carga. Criar com os endereços IP públicos [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O exemplo a seguir cria o IPv4 e IPv6 endereço IP público denominado *dsPublicIP_v4* e *dsPublicIP_v6* no *dsRG1* grupo de recursos:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
Para acessar suas máquinas virtuais usando uma conexão de RDP, crie um endereços IP públicos de IPV4 para as máquinas virtuais com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Criar o balanceador de carga básico

Nesta seção, você configurará o IP de front-end dual (IPv4 e IPv6) e o pool de endereços de back-end para o balanceador de carga e, em seguida, cria um balanceador de carga básico.

### <a name="create-front-end-ip"></a>Criar IP de front-end

Crie um IP de front-end com [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). O exemplo a seguir chamadas de configurações de IP de front-end IPv4 e IPv6 cria *dsLbFrontEnd_v4* e *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Configurar o pool de endereços de back-end

Crie um pool de endereços de back-end com [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). As VMs são anexadas a este pool de back-end nas etapas restantes. O exemplo a seguir cria pools de endereços de back-end denominados *dsLbBackEndPool_v4* e *dsLbBackEndPool_v6* para incluir as VMs com configurações de IPV4 e IPv6 NIC:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Defina a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Para tornar Íntegro certeza de que apenas VMs receberem o tráfego, opcionalmente, você pode definir uma investigação de integridade. Balanceador de carga básico usa uma investigação de IPv4 para avaliar a integridade de pontos de extremidade IPv4 e IPv6 nas VMs. Balanceador de carga padrão inclui suporte para IPv6 explicitamente as investigações de integridade.

Crie uma regra de balanceador de carga com [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). O exemplo a seguir cria regras de Balanceador de carga denominadas *dsLBrule_v4* e *dsLBrule_v6* e equilibra o tráfego *TCP* porta *80* para os IPv4 e IPv6 front-end configurações de IP:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Crie o Balanceador de Carga Básico com [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). O exemplo a seguir cria um Load Balancer básico público denominado *myLoadBalancer* usar as configurações de IP de front-end IPv4 e IPv6, pools de back-end, investigações de integridade, balanceamento de carga de regras e NAT regras que criou o etapas anteriores:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Criar recursos da rede
Antes de implantar algumas VMs e poder testar o balanceador, você deve criar recursos de rede de suporte - conjunto de disponibilidade, o grupo de segurança de rede, a rede virtual e NICs virtuais. 
### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade
Para melhorar a alta disponibilidade do seu aplicativo, coloque suas VMs em um conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). O exemplo a seguir cria um conjunto de disponibilidade chamado *myAvailabilitySet*:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede para as regras que controlará a comunicação de entrada e saída em sua rede virtual.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Criar uma regra do grupo de segurança de rede para a porta 3389

Crie uma regra do grupo de segurança de rede para permitir conexões RDP pela porta 3389 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Criar uma regra do grupo de segurança de rede para a porta 80

Criar uma regra de grupo de segurança de rede para permitir conexões de internet por meio da porta 80 com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVnet* com uma sub-rede chamada *mySubnet*:

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Criar NICs

Criar NICs virtuais com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). O exemplo a seguir cria duas NICs virtuais ambas as com as configurações de IPv4 e IPv6. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir).

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Defina o nome de usuário e a senha de um administrador para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Agora, é possível criar as VMs com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria duas VMs e os componentes de rede virtual necessários, caso ainda não existam. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2016-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Determinar os endereços IP dos pontos de extremidade IPv4 e IPv6
Obter todos os objetos de Interface de rede no grupo de recursos para resumir o IP usado nessa implantação com `get-AzNetworkInterface`. Além disso, obtenha os endereços de front-end do balanceador de carga dos pontos de extremidade IPv4 e IPv6 com `get-AzpublicIpAddress`.

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
A figura a seguir mostra um exemplo de saída que lista os endereços de IPv4 e IPv6 privados das duas VMs e os endereços IP IPv4 e IPv6 de front-end do balanceador de carga.

![Resumo IP de implantação de aplicativos de pilha dual (IPv4/IPv6) no Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Rede virtual de pilha dupla do IPv6 de modo de exibição no portal do Azure
Você pode exibir a rede virtual de pilha dupla do IPv6 no portal do Azure da seguinte maneira:
1. Na barra de pesquisa do portal, insira *dsVnet*.
2. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o. Isso inicia o **visão geral** página da rede virtual de pilha dupla denominada *dsVnet*. A rede virtual de pilha dupla mostra as duas NICs com configurações de IPv4 e IPv6 localizadas na sub-rede de pilha dupla denominada *dsSubnet*.

  ![Rede virtual de pilha dupla de IPv6 no Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> O IPv6 para a rede virtual do Azure está disponível no portal do Azure em somente leitura para esta versão de visualização.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um balanceador de carga básico com uma configuração de IP de front-end dual (IPv4 e IPv6). Você também criou um duas máquinas virtuais que incluídos NICs com duas configurações de IP (IPV4 + IPv6) que foram adicionadas ao pool de back-end do balanceador de carga. Para saber mais sobre o suporte ao IPv6 em redes virtuais do Azure, consulte [What ' s IPv6 para a rede Virtual do Azure?](ipv6-overview.md)