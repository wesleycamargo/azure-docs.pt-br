---
title: "Redes virtuais e máquinas virtuais do Windows no Azure | Microsoft Docs"
description: "Saiba mais sobre redes e como elas se relacionam aos fundamentos da criação de máquinas virtuais do Windows no Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5493e9f7-7d45-4e98-be9a-657a53708746
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: cda53c43d4524ddcc8139f60f6b605a1f26c2658
ms.lasthandoff: 04/27/2017


---

# <a name="virtual-networks-and-windows-virtual-machines-in-azure"></a>Redes virtuais e máquinas virtuais do Windows no Azure 

Ao criar uma VM (máquina virtual) do Azure, você deve criar uma [VNet](../../virtual-network/virtual-networks-overview.md) (rede virtual) ou usar uma VNet existente. Você também precisa decidir como suas VMs devem ser acessadas na VNet. É importante [planejar antes de criar recursos](../../virtual-network/virtual-network-vnet-plan-design-arm.md) e compreender os [limites de recursos de rede](../../azure-subscription-service-limits.md#networking-limits).

Na figura a seguir, as VMs são representadas como servidores Web e servidores de banco de dados. Cada conjunto de VMs é atribuído a sub-redes separadas na VNet.

![rede virtual do Azure](./media/network-overview/vnetoverview.png)

Você pode criar uma VNet antes de criar uma VM ou pode criar a VNet ao criar uma VM. Você deve criar uma rede virtual por conta própria ou ela será criada quando você criar uma VM.

Você cria esses recursos para dar suporte à comunicação com uma VM:

- Interfaces de rede
- Endereços IP
- Rede virtual e sub-redes

Além desses recursos básicos, você também deve considerar estes recursos opcionais:

- Grupos de segurança de rede
- Balanceadores de carga 

## <a name="network-interfaces"></a>Interfaces de rede

Uma [NIC (interface de rede)](../../virtual-network/virtual-network-network-interface.md) é a interconexão entre uma VM e uma VNet (rede virtual). Uma VM deve ter pelo menos uma NIC, mas pode ter mais de uma, dependendo do tamanho da VM que você criar. Saiba mais sobre para quantas NICs cada tamanho de VM dá suporte em [Tamanhos das máquinas virtuais no Azure](sizes.md). 

Se quiser criar uma VM com mais de uma NIC, você deverá criar a VM com pelo menos duas.  Após a criação, você pode adicionar NICs adicionais até o número com suporte pelo tamanho da VM, mas não pode adicionar NICs a uma VM criada apenas com um, independentemente do número de NICs ao qual tamanho da VM dá suporte. 

Se a VM for adicionada a um conjunto de disponibilidade, VMs no conjunto de disponibilidade deverão ter uma ou várias NICs. Não é necessário ter o mesmo número de NICs em VMs com mais de uma NIC, mas elas devem ter pelo menos duas.

Cada NIC conectada a uma VM deve existir no mesmo local e assinatura que a VM. Cada NIC deve ser conectada a uma VNet que existe na mesma localização e assinatura do Azure que a NIC. Depois que uma NIC é criada, você pode alterar a sub-rede à qual ela está conectada, mas você não pode alterar a VNet à qual ela está conectada.  É atribuído um endereço MAC a cada NIC conectada a uma VM, e ele não é alterado até que a VM seja excluída.

Esta tabela lista os métodos que você pode usar para criar uma interface de rede.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | Quando você cria uma VM no portal do Azure, uma interface de rede é criada automaticamente para você (não é possível usar uma NIC que você cria separadamente). O portal cria uma VM com apenas uma NIC. Se quiser criar uma VM com mais de uma NIC, você deverá criá-la com um método diferente. |
| [Azure PowerShell](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md) | Use [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) com o parâmetro **-PublicIpAddressId** para fornecer o identificador do endereço IP público que você criou anteriormente. |
| [CLI do Azure](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md) | Para fornecer o identificador do endereço IP público que você criou anteriormente, use [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) com o parâmetro **--public-ip-address**. |
| [Modelo](../../virtual-network/virtual-network-deploy-multinic-arm-template.md) | Use [Interface de rede em uma rede Virtual com endereço IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) como um guia para a implantação de uma interface de rede usando um modelo. |

## <a name="ip-addresses"></a>Endereços IP 

Você pode atribuir estes tipos de [endereços IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) a uma NIC no Azure:

- **Endereços IP públicos** - usados para comunicação de entrada e saída (sem NAT (conversão de endereços de rede)) com a Internet e outros recursos do Azure não conectados a uma VNet. Atribuir um endereço IP público a uma NIC é opcional. Endereços IP públicos têm um custo nominal, e há um número máximo que pode ser usado por assinatura.
- **Endereços IP privados** - usados para comunicação em uma VNet, sua rede local e a Internet (com NAT). Você deve atribuir pelo menos um endereço IP privado a uma VM. Para saber mais sobre NAT no Azure, leia [Noções básicas sobre conexões de saída no Azure](../../load-balancer/load-balancer-outbound-connections.md).

Você pode atribuir endereços IP públicos a VMs ou balanceadores de carga voltados para a Internet. Você pode atribuir endereços IP a VMs e balanceadores de carga internos. Você atribui endereços IP a uma VM usando uma interface de rede.

Há dois métodos de alocar um endereço IP para um recurso - dinâmico ou estático. O método de alocação padrão é dinâmico, em que um endereço IP não é alocado quando ele é criado. Em vez disso, o endereço IP é alocado quando você cria uma VM ou inicia uma VM parada. O endereço IP é liberado quando você para ou exclui a VM. 

Para garantir que o endereço IP para a VM permaneça o mesmo, você pode definir o método de alocação explicitamente como estático. Nesse caso, um endereço IP é atribuído imediatamente. Ele é liberado apenas quando você exclui a VM ou altera seu método de alocação para dinâmico.
    
Esta tabela lista os métodos que você pode usar para criar um endereço IP.

| Método | Descrição |
| ------ | ----------- |
| [Portal do Azure](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Por padrão, endereços IP públicos são dinâmicos, e o endereço associado a eles pode ser alterado quando a VM é parada ou excluída. Para assegurar que a VM sempre use o mesmo endereço IP público, crie um endereço IP público estático. Por padrão, o portal atribui um endereço IP privado dinâmico a uma NIC ao criar uma VM. Você pode alterar isso para estático após a criação da VM.|
| [Azure PowerShell](../../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Você usa [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) com o parâmetro **-AllocationMethod** como Dinâmico ou Estático. |
| [CLI do Azure](../../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Você usa [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) com o parâmetro **--allocation-method** como Dinâmico ou Estático. |
| [Modelo](../../virtual-network/virtual-network-deploy-static-pip-arm-template.md) | Use [Interface de rede em uma rede Virtual com endereço IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) como um guia para implantar um IP público de endereço usando um modelo. |

Após criar um endereço IP público, você pode associá-lo a uma VM, atribuindo-o a uma NIC.

## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes

Uma sub-rede é um intervalo de endereços IP na rede virtual. Você pode dividir a VNet em várias sub-redes para fins de organização e segurança. Cada NIC em uma VM está conectada a uma sub-rede em uma VNet. NICs conectadas às sub-redes (iguais ou diferentes) em uma VNet podem se comunicar entre si sem nenhuma configuração adicional.

Ao configurar uma rede virtual, você pode especificar a topologia, incluindo os espaços de endereço e sub-redes disponíveis. Se a VNet is precisar ser conectada a outras VNets ou redes locais, você deverá selecionar intervalos de endereços que não se sobreponham. Os endereços IP são particulares e não podem ser acessados pela Internet, o que só se aplicava aos endereços IP não roteáveis, como 10.0.0.0/8, 172.16.0.0/12 ou 192.168.0.0/16. Agora, o Azure trata qualquer intervalo de endereços como parte do espaço de endereço IP VNet particular que está acessível apenas através da VNet, em VNets interconectadas e do seu local. 

Se trabalha em uma organização em que outra pessoa é responsável por redes internas, você deve conversar com essa pessoa antes de selecionar o espaço de endereço. Verifique se não há sobreposição e informe o espaço que deseja usar para que eles não tentem usar o mesmo intervalo de endereços IP. 

Por padrão, não há limite de segurança entre sub-redes. Portanto, as VMs em cada uma das sub-redes podem se comunicar entre si. No entanto, você pode configurar NSGs (Grupos de Segurança de Rede), que permitem controlar o fluxo de tráfego de e para sub-redes e de e para VMs. 

Esta tabela lista os métodos que você pode usar para criar uma VNet e sub-redes.    

| Método | Descrição |
| ------ | ----------- |
| [Portal do Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md) | Se você permitir que o Azure crie uma VNet quando você criar uma VM, o nome será uma combinação do nome do grupo de recursos que contém a VNet e **-vnet**. O espaço de endereço é 10.0.0.0/24, o nome da sub-rede necessária é **default** e o intervalo de endereços de sub-rede é 10.0.0.0/24. |
| [Azure PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md) | Você usa [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmVirtualNetworkSubnetConfig) e [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmVirtualNetwork) para criar uma sub-rede e uma VNet. Você também pode usar [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) para adicionar uma sub-rede a uma rede virtual existente. |
| [CLI do Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md) | A sub-rede e a VNet são criadas ao mesmo tempo. Forneça um parâmetro **--subnet-name** para [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) com o nome da sub-rede. |
| [Modelo](../../virtual-network/virtual-networks-create-vnet-arm-template-click.md) | A maneira mais fácil de criar uma VNet e sub-redes é baixar um modelo existente, como [Rede Virtual com duas sub-redes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), e modificá-lo de acordo com suas necessidades. |

## <a name="network-security-groups"></a>Grupos de segurança de rede

Um [NSG (grupo de segurança de rede)](../../virtual-network/virtual-networks-nsg.md) contém uma lista de regras de ACL (Lista de Controle de Acesso) que permitem ou negam o tráfego de rede para sub-redes, NICs ou ambos. Os NSGs podem ser associados a sub-redes ou NICs individuais conectadas a uma sub-rede. Quando um NSG é associado a uma sub-rede, as regras de ACL se aplicam a todas as VMs na sub-rede. Além disso, o tráfego para uma NIC individual pode ser restringido associando um NSG diretamente a uma NIC.

Os NSGs contêm dois conjuntos de regras: entrada e saída. A prioridade de uma regra deve ser exclusiva em cada conjunto. Cada regra tem propriedades de protocolo, origem e intervalos de porta de destino, prefixos de endereço, direção de tráfego, prioridade e tipo de acesso. 

Todos os NSGs contêm um conjunto de regras padrão. As regras padrão não podem ser excluídas, mas como recebem a prioridade mais baixa, elas podem ser substituídas pelas regras que você criar. 

Quando um NSG é associado a uma NIC, as regras de acesso à rede no NSG são aplicadas somente a essa NIC. Se um NSG for aplicado a uma única NIC em uma VM com várias NICS, isso não afetará o tráfego para as outras NICs. É possível associar diferentes NSGs a um NIC (ou VM, dependendo do modelo de implantação) e à sub-rede a qual uma NIC ou VM está associada. A prioridade é fornecida com base na direção do tráfego.

Não se esqueça de [planejar](../../virtual-network/virtual-networks-nsg.md#planning) seus NSGs ao planejar suas VMs e a VNet.

Esta tabela lista os métodos que você pode usar para criar um grupo de segurança de rede.

| Método | Descrição |
| ------ | ----------- |
| [Portal do Azure](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md) | Quando você cria uma VM no portal do Azure, um NSG é criado e associado automaticamente à NIC que o portal cria. O nome do NSG é uma combinação do nome da VM e **-nsg**. Este NSG contém uma regra de entrada com uma prioridade de 1000, o serviço definido como RDP, o protocolo definido como TCP, a porta configurada para 3389 e as ações definidas para Permitir. Se quiser permitir qualquer outro tráfego de entrada para a VM, você deverá incluir regras adicionais para o NSG. |
| [Azure PowerShell](../../virtual-network/virtual-networks-create-nsg-arm-ps.md) | Use [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkSecurityRuleConfig) e forneça as informações de regra necessárias. Use [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkSecurityGroup) para criar o NSG. Use [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/Set-AzureRmVirtualNetworkSubnetConfig) para configurar o NSG da sub-rede. Use [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) para adicionar o NSG à rede virtual. |
| [CLI do Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md) | Use [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) para criar inicialmente o NSG. Use [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) para adicionar regras para o NSG. Use [az network vnet subnet update](https://docs.microsoft.com/en-us/cli/azure/network/vnet/subnet#update) para adicionar o NSG à sub-rede. |
| [Modelo](../../virtual-network/virtual-networks-create-nsg-arm-template.md) | Use [Criar um grupo de segurança de rede](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) como um guia para a implantação de um grupo de segurança de rede usando um modelo. |

## <a name="load-balancers"></a>Balanceadores de carga

O [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) oferece alta disponibilidade e desempenho de rede para seus aplicativos. Um balanceador de carga pode ser configurado para [equilibrar o tráfego da Internet](../../load-balancer/load-balancer-internet-overview.md) para VMs ou [equilibrar o tráfego entre VMs em uma rede virtual](../../load-balancer/load-balancer-internal-overview.md). Um balanceador de carga também pode equilibrar o tráfego entre computadores locais e VMs em uma rede entre locais ou encaminhar tráfego externo para uma VM específica.

O balanceador de carga mapeia o tráfego de entrada e saída entre o endereço IP público e a porta do balanceador de carga e o endereço IP privado e a porta da VM.

Ao criar um balanceador de carga, você também deve considerar estes elementos de configuração:

- **Configuração de IP front-end** – um balanceador de carga pode incluir um ou mais endereços IP front-end, também conhecidos como VIPs (IPs virtuais). Esses endereços IP servem como entrada para o tráfego.
- **Pool de endereços de back-end** – endereços IP que estão associados à NIC para a qual a carga é distribuída.
- **Regras de NAT** - define como o tráfego flui pelo IP de front-end e é distribuído para o IP de back-end.
- **Regras do balanceador de carga** -mapeia determinada combinação de porta e IP front-end para um conjunto de endereços IP back-end e combinação de portas. Um balanceador de carga único pode ter várias regras de balanceamento de carga. Cada regra é uma combinação de um IP de front-end e IP de porta e back-end e porta associada a VMs.
- **[Sondas](../../load-balancer/load-balancer-custom-probe-overview.md)**  - monitora a integridade das VMs. Quando uma investigação não responde, o balanceador de carga interrompe o envio de novas conexões para a VM não íntegra. As conexões existentes não são afetadas e novas conexões são enviadas para VMs íntegras.

Esta tabela lista os métodos que você pode usar para criar um balanceador de carga voltado para a Internet.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | No momento, você não pode criar um balanceador de carga voltado para a Internet usando o portal do Azure. |
| [Azure PowerShell](../../load-balancer/load-balancer-get-started-internet-arm-ps.md) | Para fornecer o identificador do endereço IP público que você criou anteriormente, use [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerFrontendIpConfig) com o parâmetro **-PublicIpAddress**. Use [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerBackendAddressPoolConfig) para criar a configuração do pool de endereços de back-end. Use [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerInboundNatRuleConfig) para criar regras NAT de entrada associadas à configuração de IP front-end que você criou. Use [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerProbeConfig) para criar os testes de que você precisa. Use [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerRuleConfig) para criar a configuração de balanceador de carga. Use [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) para criar o balanceador de carga.|
| [CLI do Azure](../../load-balancer/load-balancer-get-started-internet-arm-cli.md) | Use [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) para criar a configuração de balanceador de carga inicial. Use [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) para adicionar o endereço IP público que você criou anteriormente. Use [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) para adicionar a configuração do pool de endereços de back-end. Use [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) para adicionar regras de NAT. Use [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) para adicionar as regras do balanceador de carga. Use [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) para adicionar as sondas. |
| [Modelo](../../load-balancer/load-balancer-get-started-internet-arm-template.md) | Use [Duas VMs em um balanceador de carga e regras de NAT configuradas no balanceamento de carga](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-natrules) como um guia para a implantação de um balanceador de carga usando um modelo. |
    
Esta tabela lista os métodos que você pode usar para criar um balanceador de carga interno.

| Método | Descrição |
| ------ | ----------- |
| Portal do Azure | No momento, você não pode criar um balanceador de carga interno usando o portal do Azure. |
| [Azure PowerShell](../../load-balancer/load-balancer-get-started-ilb-arm-ps.md) | Para fornecer um endereço IP privado na sub-rede, use [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerFrontendIpConfig) com o parâmetro **-PrivateIpAddress**. Use [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerBackendAddressPoolConfig) para criar a configuração do pool de endereços de back-end. Use [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerInboundNatRuleConfig) para criar regras NAT de entrada associadas à configuração de IP front-end que você criou. Use [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerProbeConfig) para criar os testes de que você precisa. Use [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerRuleConfig) para criar a configuração de balanceador de carga. Use [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) para criar o balanceador de carga.|
| [CLI do Azure](../../load-balancer/load-balancer-get-started-ilb-arm-cli.md) | Use o comando [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) para criar a configuração de balanceador de carga inicial. Para definir o endereço IP privado, use [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) com o parâmetro **--private-ip-address**. Use [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) para adicionar a configuração do pool de endereços de back-end. Use [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) para adicionar regras de NAT. Use [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) para adicionar as regras do balanceador de carga. Use [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) para adicionar as sondas.|
| [Modelo](../../load-balancer/load-balancer-get-started-ilb-arm-template.md) | Use [Duas VMs em um balanceador de carga e regras de NAT configuradas no balanceamento de carga](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) como um guia para a implantação de um balanceador de carga usando um modelo. |

## <a name="vms"></a>VMs

As VMs podem ser criadas na mesma VNet e podem se conectar entre si usando endereços IP privados. Eles podem se conectar mesmo que estejam em sub-redes diferentes, sem a necessidade de configurar um gateway ou usar endereços IP públicos. Para colocar as VMs em uma rede virtual, crie a rede virtual e, ao criar cada VM, atribua-a à VNet e à sub-rede. As VMs adquirem suas configurações de rede durante a implantação ou a inicialização.  

Um endereço IP é atribuído às VMs quando elas são implantadas. Se você implantar várias VMs em uma rede virtual ou sub-rede, endereços IP serão atribuídos quando elas forem inicializadas. Um DIP (endereço IP dinâmico) é o endereço IP interno associado a uma VM. Você pode alocar um DIP estático para uma VM. Se alocar um DIP estático, você deverá considerar o uso de uma sub-rede específica para evitar a reutilização acidental de um DIP estático para outra VM.  

Se você criar uma VM e, depois, quiser migrá-la para uma rede virtual, essa não será uma alteração simples na configuração. Você deve reimplantar a VM na VNet. A maneira mais fácil der reimplantar é excluir a VM, mas não os discos anexados a ela e, em seguida, recriar a VM usando os discos originais na VNet. 

Esta tabela lista os métodos que você pode usar para criar uma VM em uma VNet.

| Método | Descrição |
| ------ | ----------- |
| [Portal do Azure](../virtual-machines-windows-hero-tutorial.md) | Usa as configurações de rede padrão mencionadas anteriormente para criar uma VM com uma única NIC. Para criar uma VM com várias NICs, você deve usar um método diferente. |
| [Azure PowerShell](../virtual-machines-windows-ps-create.md) | Inclui o uso de [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) para adicionar o NIC que você criou anteriormente para a configuração da VM. |
| [Modelo](ps-template.md) | Use [Implantação muito simples de uma VM do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) como um guia para implantar uma VM usando um modelo. |

## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar [rotas definidas pelo usuário e encaminhamento IP](../../virtual-network/virtual-networks-udr-overview.md). 
- Saiba como configurar [conexões de VNet para VNet](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Saiba como [Solucionar problemas de rotas](../../virtual-network/virtual-network-routes-troubleshoot-portal.md).

