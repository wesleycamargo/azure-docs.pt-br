<properties 
   pageTitle="Criar um balanceador de carga para a Internet no Gerenciador de Recursos usando o PowerShell | Microsoft Azure"
   description="Saiba como criar um balanceador de carga para a Internet no Gerenciador de Recursos usando o PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/24/2015"
   ms.author="joaoma" />

# Introdução à criação de um balanceador de carga para a Internet no Gerenciador de Recursos usando o PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Também é possível [Saber como criar um balanceador de carga para a Internet usando o modelo de implantação clássica](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

As etapas a seguir mostram como criar um balanceador de carga para a Internet usando o gerenciador de recursos do Azure com o PowerShell. Com o Gerenciador de Recursos do Azure, os itens para criar um balanceador de carga para a Internet são configurados individualmente e, em seguida, colocados juntos para criar um recurso.

Isto abordará a sequência de tarefas individuais que precisam ser realizadas para a criação de um balanceador de carga e explicará em detalhes o que é feito para atingir esse objetivo.

## O que é necessário para criar um balanceador de carga para a Internet?

Você precisa criar e configurar os seguintes objetos para implantar um balanceador de carga:

- Configuração de IP de front-end – contém endereços IP públicos para o tráfego de rede de entrada. 

- Pool de endereços de back-end – contém NICs (interfaces de rede) para que as máquinas virtuais recebam o tráfego de rede do balanceador de carga.

- Regras de balanceamento de carga – contém regras de mapeamento de uma porta pública no balanceador de carga para uma porta no pool de endereços de back-end.

- Regras NAT de entrada – contém regras de mapeamento de uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no pool de endereços de back-end.

- Investigações – contém investigações de integridade usadas para verificar a disponibilidade de instâncias de máquinas virtuais no pool de endereços de back-end.

É possível obter mais informações sobre componentes do balanceador de carga com o gerenciador de recursos do Azure em [Suporte do Gerenciador de Recursos do Azure para o balanceador de carga](load-balancer-arm.md).


## Configurar o PowerShell para usar o Gerenciador de Recursos

Certifique-se de ter a versão de produção mais recente do módulo do Azure para PowerShell e de configurar corretamente o PowerShell para acessar sua assinatura do Azure.

### Etapa 1

		PS C:\> Login-AzureRmAccount



### Etapa 2

Verificar as assinaturas da conta

		PS C:\> get-AzureRmSubscription 

Você deverá se autenticar com suas credenciais.<BR>

### Etapa 3 

Escolha quais das suas assinaturas do Azure deseja usar.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Etapa 4

Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

    PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"


## Criar uma rede virtual e um endereço IP público para o pool de IPs de front-end

### Etapa 1

Criar uma sub-rede e uma rede virtual.

	$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Etapa 2

Crie um PIP (endereço IP público) chamado *PublicIP* a ser usado por um pool de IPs de front-end com o nome DNS *loadbalancernrp.westus.cloudapp.azure.com*. O comando a seguir usa o tipo de alocação estática.

	$publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]O balanceador de carga usará o rótulo de domínio do IP público como prefixo para seu FQDN. Essa é uma mudança do modelo de implantação clássica, que usa o serviço de nuvem como o FQDN do balanceador de carga. Neste exemplo, o FQDN será *loadbalancernrp.westus.cloudapp.azure.com*.

## Criar um pool de IPs front-end e um pool de endereços back-end

### Etapa 1 

Crie um pool de IPs de front-end chamado *LB-Frontend* que usa o PIP *PublicIp*.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Etapa 2 

Crie um pool de endereços de back-end chamado *LB-backend*.

	$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## Criar regras de LB, regras NAT, um teste e um balanceador de carga

O exemplo a seguir cria os seguintes itens:

- uma regra NAT para converter todo o tráfego de entrada na porta 3441 para a porta 3389.
- uma regra NAT para converter todo o tráfego de entrada na porta 3442 para a porta 3389.
- uma regra de balanceador de carga para equilibrar todo o tráfego de entrada na porta 80 para a porta 80 de endereços no pool de back-end.
- uma regra de teste que verifica o status de integridade em uma página denominada *HealthProbe.aspx*.
- um balanceador de carga que usa todos os objetos acima.



### Etapa 1

Criar regras NAT.

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Etapa 2

Crie uma regra de balanceador de carga.

	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Etapa 3

Crie um teste de integridade.

	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Etapa 4

Criar o balanceador de carga usando os objetos criados acima.

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## Criar NICs

Você precisa criar NICs (ou modificar as existentes) e associá-las a regras NAT, regras do balanceador de carga e testes.

### Etapa 1 

Obter a VNet e a sub-rede na qual as NICs devem ser criadas.

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Etapa 2

Crie uma NIC chamada *lb-nic1-be* e associe-a à primeira regra NAT e ao primeiro (e único) pool de endereços de back-end.
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Etapa 3

Crie uma NIC chamada *lb-nic2-be* e associe-a à segunda regra NAT e ao primeiro (e único) pool de endereços de back-end.

	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Etapa 4

Conferir as NICs.


	PS C:\> $backendnic1

Saída esperada:

	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### Etapa 5

Use o cmdlet `Add-AzureRmVMNetworkInterface` para atribuir as NICs a VMs diferentes.

Você pode encontrar orientações sobre como criar uma máquina virtual e atribuir uma NIC em [Criar e pré-configurar uma Máquina Virtual do Windows com o Gerenciador de Recursos e o Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example), usando a opção 5 no exemplo.

## Atualizar um balanceador de carga existente


### Etapa 1

Usando o balanceador de carga do exemplo acima, atribua o objeto balanceador de carga à variável $slb usando Get-AzureLoadBalancer

	$slb=get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Etapa 2

No exemplo a seguir, você adicionará uma nova regra de NAT de entrada usando a porta 81 no front-end e a porta 8181 do pool de back-end a um balanceador de carga existente

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Etapa 3

Salvar a nova configuração usando Set-AzureLoadBalancer

	$slb | Set-AzureRmLoadBalancer

## Remover um balanceador de carga

Use o comando Remove-AzureLoadBalancer para excluir um balanceador de carga criado anteriormente denominado "NRP-LB" em um grupo de recursos chamado " "NRP-RG"

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]Você pode usar a opção -Force para evitar a solicitação de exclusão.

## Próximas etapas

[Introdução à configuração de um balanceador de carga interno](load-balancer-internal-getstarted.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1125_2015-->