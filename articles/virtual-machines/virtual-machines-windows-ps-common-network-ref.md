<properties
	pageTitle="Comandos de rede comuns do PowerShell para VMs | Microsoft Azure"
	description="Comandos comuns do PowerShell para ajudar você a criar uma rede virtual e seus recursos associados para VMs."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="davidmu"/>

# Comandos de rede comuns do Azure PowerShell para VMs

Se você quiser criar uma máquina virtual, será necessário criar uma [rede virtual](../virtual-network/virtual-networks-overview.md) ou saber sobre uma rede virtual existente na qual a VM possa ser adicionada. Normalmente, quando você cria uma VM, você também precisa considerar a criação dos recursos descritos neste artigo.

## Criar recursos usando o Azure PowerShell

Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura que você deseja usar e entrar na sua conta do Azure.

Recurso | Command 
-------------- | -------------------------
Sub-rede | $internetSubnet = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name internetSubnet -AddressPrefix 10.0.1.0/24<BR>$internalSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name internalSubnet -AddressPrefix 10.0.2.0/24<BR><BR>Uma rede típica pode ter uma sub-rede para um [balanceador de carga de rede voltado para a internet](../load-balancer/load-balancer-internet-overview.md) e uma sub-rede separada para um [balanceador de carga interna](../load-balancer/load-balancer-internal-overview.md). |
Rede virtual | Criar uma rede virtual:<BR><BR>$rgName = "[resource-group-name](../powershell-azure-resource-manager.md)"<BR>$locName = "[location-name](https://msdn.microsoft.com/library/azure/dn495177.aspx)"<BR>$vnetName = "virtual-network-name"<BR>$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $internetSubnet,$internalSubnet<BR><BR>Obter uma lista de redes virtuais:<BR><BR>[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName $rgName | Sort Name | Select Name<BR><BR>Listar as sub-redes em uma rede virtual:<BR><BR>Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets<BR><BR>Você deve ver algo como o seguinte mostrando a lista de sub-redes:<BR><BR>Sub-redes<BR>-------<BR>{internetSubnet, internalSubnet}<BR><BR>O índice de sub-rede para a internetSubnet é 0 e o índice de sub-rede para a internalSubnet is 1.
Rótulo do nome de domínio | $domName = "domain-name"<BR>[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName $domName -Location $locName<BR><BR>Você pode especificar um rótulo de nome de domínio DNS para [um recurso IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) que cria um mapeamento para domainnamelabel.location.cloudapp.azure.com para o endereço IP público nos servidores DNS gerenciados pelo Azure. O rótulo pode conter apenas letras, números e hifens. O primeiro e último caractere devem ser uma letra ou número, e o rótulo de nome de domínio deve ser exclusivo dentro de seu local do Azure. Sempre teste se o seu rótulo de nome de domínio é globalmente exclusivo. Se **True** retornar, o nome proposto será globalmente exclusivo.
Endereço IP público | $ipName = "public-ip-address-name"<BR>$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name $ipName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic<BR><BR>O endereço IP público usa o rótulo de nome de domínio criado anteriormente, e é usado pela configuração de front-end do balanceador de carga.
Configuração do IP front-end | $feConfigName = "frontend-ip-config-name"<BR>$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name $feConfigName -PublicIpAddress $pip<BR><BR>A configuração de front-end inclui o endereço IP público criado anteriormente para o tráfego de rede de entrada.
Pool de endereços de back-end | $bePoolName = "back-end-pool-name"<BR>$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name $bePoolName<BR><BR>Fornece endereços internos para o back-end do balanceador de carga que são acessados por meio de uma interface de rede.
Investigação | $probeName = "health-probe-name"<BR>$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name $probeName -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contém as investigações de integridade usadas para verificar a disponibilidade das instâncias de máquina virtual no pool de endereços back-end.
Regra de balanceamento de carga | $lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contém regras que mapeiam uma porta pública no balanceador de carga para uma porta no pool de endereços back-end.
Regra NAT de entrada | $ruleName = "NAT-rule-name"<BR>$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name $ruleName -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contém regras que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no pool de endereços back-end.
Balanceador de carga | $lbName = "load-balancer-name"<BR>$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName $rgName -Name $lbName -Location $locName -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe
Interface de rede | $vnet = [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -Name $vnetName -ResourceGroupName $rgName<BR>$internalSubnet = [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "internalSubnet" -VirtualNetwork $vnet<BR>$nicName = "network-interface-name"<BR>$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName $rgName -Name $nicName -Location $locName -PrivateIpAddress 10.0.2.6 -Subnet $internalSubnet -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crie uma interface de rede usando o endereço IP público e a sub-rede de rede virtual criada anteriormente.
	
## Próximas etapas

- Usar a interface de rede que você acabou de criar durante a [criação de uma VM](virtual-machines-windows-ps-create.md).
- Saiba mais sobre como você pode [criar uma VM com várias interfaces de rede](../virtual-network/virtual-networks-multiple-nics.md).

<!---HONumber=AcomDC_0615_2016-->