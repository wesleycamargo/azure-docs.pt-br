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
   ms.date="10/21/2015"
   ms.author="joaoma" />

# Criar um balanceador de carga para a Internet no Gerenciador de Recursos usando o PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação do Gerenciador de Recursos.

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

As etapas a seguir mostram como criar um balanceador de carga para a Internet usando o gerenciador de recursos do Azure com o PowerShell. Com o Gerenciador de Recursos do Azure, os itens para criar um balanceador de carga para a Internet são configurados individualmente e, em seguida, colocados juntos para criar um recurso.

Nesta página, abordaremos a sequência de tarefas individuais que precisam ser realizadas para a criação de um balanceador de carga e explicaremos em detalhes o que está sendo feito para atingir o objetivo de criar um balanceador de carga.

## O que é necessário para criar um balanceador de carga para a Internet?

Você precisa criar e configurar os objetos a seguir para implantar um balanceador de carga.

- Configuração de IP front-end – contém endereços IP públicos para o tráfego de rede de entrada. 

- Pool de endereços back-end – contém NICs (Interfaces de Rede) para receber o tráfego do balanceador de carga.

- Regras de balanceamento de carga – contém regras de mapeamento de uma porta pública no balanceador de carga para portas nas NICs no pool de endereços de back-end.

- Regras NAT de entrada – contém regras de mapeamento de uma porta pública no balanceador de carga para portas nas NICs no pool de endereços de back-end.

- Testes – contém investigações de integridade usadas para verificar a disponibilidade de VMs vinculadas a NICs no pool de endereços de back-end.

É possível obter mais informações sobre componentes do balanceador de carga com o gerenciador de recursos do Azure em [Suporte do Gerenciador de Recursos do Azure para balanceador de carga](load-balancer-arm.md).


## Configurar o PowerShell para usar o Gerenciador de Recursos
Certifique-se de ter a versão de produção mais recente do módulo do Azure para PowerShell e de configurar corretamente o PowerShell para acessar sua assinatura do Azure.

### Etapa 1

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](powershell-install-configure.md) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.
2. Em um prompt do Azure PowerShell, execute o cmdlet **Switch-AzureMode** para alternar para o modo Gerenciador de Recursos, como mostrado abaixo.

		Switch-AzureMode AzureResourceManager
	
	Saída esperada:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING]O cmdlet Switch-AzureMode será preterido em breve. Quando isso acontecer, todos os cmdlets do Gerenciador de Recursos serão renomeados.



### Etapa 2

 Se você nunca usou o Azure PowerShell, veja [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md) e siga as instruções até o fim para entrar no Azure e escolher sua assinatura. Em um prompt do Azure PowerShell, execute o cmdlet **Switch-AzureMode** para alternar para o modo Gerenciador de Recursos, como mostrado abaixo.

		Switch-AzureMode AzureResourceManager
	
	Expected output:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING]O cmdlet Switch-AzureMode será preterido em breve. Quando isso acontecer, todos os cmdlets do Gerenciador de Recursos serão renomeados.


### Etapa 3

Faça logon na sua conta do Azure.


    PS C:\> Add-AzureAccount

Você deverá autenticar com suas credenciais.


### Etapa 4

Escolha quais das suas assinaturas do Azure deseja usar.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Para ver uma lista das assinaturas disponíveis, use o cmdlet “Get-AzureSubscription”.

## Criar um grupos de recursos

Crie um novo grupo de recursos denominado *NRP-RG* em um local do Azure no *Oeste dos EUA*.

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

## Criar uma rede virtual e um endereço IP público para o pool de IPs front-end

### Etapa 1

Criar uma sub-rede e uma rede virtual.

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Etapa 2

Criar um PIP (Endereço IP Público) denominado *PublicIP* a ser usado por um pool de IPs front-end com nome DNS *loadbalancernrp.westus.cloudapp.azure.com*. O comando a seguir usa o tipo de alocação estática.

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]O balanceador de carga usará o rótulo de domínio do IP público como FQDN. Isso é uma mudança da implantação clássica, que usa serviço de nuvem como FQDN do balanceador de carga. Neste exemplo, o FQDN será *loadbalancernrp.westus.cloudapp.azure.com*.

## Criar um pool de IPs front-end e um pool de endereços back-end

### Etapa 1 

Criar um pool de IPs front-end denominado *Front-end de LB* que usa o PIP *PublicIp*.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### etapa 2: 

Criar um pool de endereços back-end denominado *back-end de LB*.

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## Criar regras de LB, regras NAT, um teste e um balanceador de carga

O exemplo a seguir cria os seguintes itens:

- uma regra NAT para converter todo o tráfego de entrada na porta 3441 para a porta 3389.
- uma regra NAT para converter todo o tráfego de entrada na porta 3442 para a porta 3389.
- uma regra de balanceador de carga para equilibrar todo o tráfego de entrada na porta 80 para a porta 80 de endereços no pool de back-end.
- uma regra de investigação que verificará o status de integridade em uma página denominada *HealthProbe.aspx*.
- um balanceador de carga que usa todos os objetos acima.

### Etapa 1

Criar regras NAT.

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Etapa 2

Criar uma regra de balanceador de carga.

	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Etapa 3

Criar um teste de integridade.

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Etapa 4

Criar o balanceador de carga usando os objetos criados acima.

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## Criar NICs

Você precisa criar NICs (ou modificar as existentes) e associá-las a regras NAT, regras do balanceador de carga e testes.

### Etapa 1 

Obter a VNet e a sub-rede na qual as NICs devem ser criadas.

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Etapa 2

Criar uma NIC chamada *lb-nic1-be*, e associá-la com a primeira regra NAT e com o primeiro (e único) pool de endereços de back-end.
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Etapa 3

Criar uma NIC chamada *lb-nic2-be*, e associá-la com a segunda regra NAT e com o primeiro (e único) pool de endereços de back-end.

	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

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

Use o cmdlet `Add-AzureVMNetworkInterface` para atribuir NICs a VMs diferentes.

Você pode encontrar orientações sobre como criar uma máquina virtual e atribuir uma NIC em [Criar e pré-configurar uma máquina virtual do Windows com o Gerenciador de Recursos e o Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example), usando a opção 5 no exemplo.

## Próximas etapas

[Introdução à configuração de um balanceador de carga interno](load-balancer-internal-getstarted.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO1-->