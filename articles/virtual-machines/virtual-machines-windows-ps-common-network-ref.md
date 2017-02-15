---
title: Comandos de rede comuns do PowerShell para VMs | Microsoft Docs
description: "Comandos comuns do PowerShell para ajudar você a criar uma rede virtual e seus recursos associados para VMs."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: ce08e9dcd0585e00bdd42b8aa34ac2c597a53a6e


---
# <a name="common-network-azure-powershell-commands-for-vms"></a>Comandos de rede comuns do Azure PowerShell para VMs
Se você quiser criar uma máquina virtual, será necessário criar uma [rede virtual](../virtual-network/virtual-networks-overview.md) ou saber sobre uma rede virtual existente na qual a VM possa ser adicionada. Normalmente, quando você cria uma VM, você também precisa considerar a criação dos recursos descritos neste artigo.

Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

## <a name="create-network-resources"></a>Criar recursos da rede
| Tarefa | Command |
| --- | --- |
| Criar configurações da sub-rede |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name "nome_da_subrede" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "nome_da_subrede" -AddressPrefix XX.X.X.X/XX<BR><BR>Uma rede típica pode ter uma sub-rede para um [balanceador de carga para a internet](../load-balancer/load-balancer-internet-overview.md) e uma sub-rede separada para um [balanceador de carga interno](../load-balancer/load-balancer-internal-overview.md). |
| Criar uma rede virtual |$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name "nome_da_rede_virtual" -ResourceGroupName "nome_do_grupo_de_recursos" -Location "nome_do_local" -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Testar um nome de domínio exclusivo |[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName "nome_do_domínio" -Location "nome_do_local"<BR><BR>Você pode especificar um nome de domínio DNS para um [recurso IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md), que cria um mapeamento de domainname.location.cloudapp.azure.com para o endereço IP público nos servidores DNS gerenciados pelo Azure. O campo pode conter apenas letras, números e hifens. O primeiro e o último caractere devem ser uma letra ou um número e o nome de domínio deve ser exclusivo no local do seu Azure. Se **True** retornar, o nome proposto será globalmente exclusivo. |
| Criar um endereço IP público |$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name "nome_do_endereço_ip" -ResourceGroupName "nome_do_grupo_de_recursos" -DomainNameLabel "nome_do_domínio" -Location "nome_do_local" -AllocationMethod Dynamic<BR><BR>O endereço IP público usa o nome de domínio criado anteriormente e é usado pela configuração de front-end do balanceador de carga. |
| Criar uma configuração de IP de front-end |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name "frontend_ip_name" -PublicIpAddress $pip<BR><BR>A configuração de front-end inclui o endereço IP público criado anteriormente para o tráfego de rede de entrada. |
| Criar um pool de endereços de back-end |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name "backend_pool_name"<BR><BR>Fornece endereços internos para o back-end do balanceador de carga que são acessados por meio de uma interface de rede. |
| Criar uma investigação |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name "probe_name" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contém as investigações de integridade usadas para verificar a disponibilidade das instâncias de máquina virtual no pool de endereços back-end. |
| Criar uma regra de balanceamento de carga |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contém regras que atribuem uma porta pública no balanceador de carga a uma porta no pool de endereços de back-end. |
| Criar uma regra NAT de entrada |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name "rule_name" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contém regras que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no pool de endereços back-end. |
| Criar um balanceador de carga |$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName "nome_do_grupo_de_recursos" -Name "nome_do_balanceador_de_carga" -Location "nome_do_local" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Criar um adaptador de rede |$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName "nome_do_grupo_de_recursos" -Name "nome_da_interface_de_rede" -Location "nome_do_local" -PrivateIpAddress XX.X.X.X -Subnet subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crie uma interface de rede usando o endereço IP público e a sub-rede de rede virtual criada anteriormente. |

## <a name="get-information-about-network-resources"></a>Obter informações sobre recursos de rede
| Tarefa | Command |
| --- | --- |
| Listar redes virtuais |[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName "nome_do_grupo_de_recursos"<BR><BR>Lista todas as redes virtuais no grupo de recursos. |
| Obter informações sobre uma rede virtual |Get-AzureRmVirtualNetwork -Name "nome_da_rede_virtual" -ResourceGroupName "nome_do_grupo_de_recursos" |
| Listar sub-redes em uma rede virtual |Get-AzureRmVirtualNetwork -Name "nome_da_rede_virtual" -ResourceGroupName "nome_do_grupo_de_recursos" &#124; Select Subnets |
| Obter informações sobre uma sub-rede |[Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "nome_da_subrede" -VirtualNetwork $vnet<BR><BR>Obtém informações sobre a sub-rede na rede virtual especificada. O valor de $vnet representa o objeto retornado por Get-AzureRmVirtualNetwork. |
| Listar endereços IP |[Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) -ResourceGroupName "nome_do_grupo_de_recursos"<BR><BR>Lista os endereços IP públicos no grupo de recursos. |
| Listar balanceadores de carga |[Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) -ResourceGroupName "nome_do_grupo_de_recursos"<BR><BR>Lista todos os balanceadores de carga no grupo de recursos. |
| Listar adaptadores de rede |[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) -ResourceGroupName "nome_do_grupo_de_recursos"<BR><BR>Lista todos os adaptadores de rede no grupo de recursos. |
| Obter informações sobre um adaptador de rede |Get-AzureRmNetworkInterface -Name "nome_da_interface_de_rede" -ResourceGroupName "nome_do_grupo_de_recursos"<BR><BR>Obtém informações sobre um adaptador de rede específico. |
| Obter a configuração de IP de um adaptador de rede |[Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -Name "nome_da_configuraçãoip" -NetworkInterface $nic<BR><BR>Obtém informações sobre a configuração de IP do adaptador de rede especificado. O valor de $nic representa o objeto retornado por Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Gerenciar recursos de rede
| Tarefa | Command |
| --- | --- |
| Adicionar uma sub-rede a uma rede virtual |[Add-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) -AddressPrefix XX.X.X.X/XX -Name "nome_da_subrede" -VirtualNetwork $vnet<BR><BR>Adiciona uma sub-rede a uma rede virtual existente. O valor de $vnet representa o objeto retornado por Get-AzureRmVirtualNetwork. |
| Excluir uma rede virtual |[Remove-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -Name "nome_da_rede_virtual" -ResourceGroupName "nome_do_grupo_de_recursos"<BR><BR>Remove a rede virtual especificada do grupo de recursos. |
| Excluir um adaptador de rede |[Remove-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -Name "nome_da_interface_de_rede" -ResourceGroupName "nome_do_grupo_de_recursos"<BR><BR>Remove o adaptador de rede especificado do grupo de recursos. |
| Excluir um balanceador de carga |[Remove-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -Name "nome_do_balanceador_de_carga" -ResourceGroupName "nome_do_grupo_de_recursos"<BR><BR>Remove o balanceador de carga especificado do grupo de recursos. |
| Excluir um endereço IP público |[Remove-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-Name "inome_do_endereço_ip" -ResourceGroupName "nome_do_grupo_de_recursos"<BR><BR>Remove o endereço IP público especificado do grupo de recursos. |

## <a name="next-steps"></a>Próximas etapas
* Usar o adaptador de rede que você cria quando [cria uma VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Saiba mais sobre como você pode [criar uma VM com várias interfaces de rede](../virtual-network/virtual-networks-multiple-nics.md).




<!--HONumber=Dec16_HO2-->


