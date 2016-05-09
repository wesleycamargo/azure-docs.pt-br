<properties
   pageTitle="Introdução à configuração de um balanceador de carga para a Internet usando o Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Como criar regras para um balanceador de carga, regras de NAT, teste para o gerenciador de recursos do Azure. Passo a passo mostra o processo de ponta a ponta para criar um recurso de balanceador de carga."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Introdução à configuração de um balanceador de carga para a Internet usando o Gerenciador de Recursos do Azure


> [AZURE.SELECTOR]
- [Etapas do Azure Clássico](load-balancer-internet-getstarted.md)
- [Etapas do PowerShell no Gerenciador de Recursos](load-balancer-arm-powershell.md)


As etapas a seguir mostram como criar um balanceador de carga para a Internet usando o gerenciador de recursos do Azure com o PowerShell. Com o Gerenciador de Recursos do Azure, os itens para criar um balanceador de carga para a Internet são configurados individualmente e, em seguida, colocados juntos para criar um recurso.

Nesta página, abordaremos a sequência de tarefas individuais que precisam ser realizadas para a criação de um balanceador de carga e explicaremos em detalhes o que está sendo feito para atingir o objetivo de criar um balanceador de carga.


## O que é necessário para criar um balanceador de carga para a Internet?

Os itens a seguir devem ser configurados antes da criação de um balanceador de carga:

- Configuração do IP de front-end - adicionará um endereço IP público ao pool de IPs do front-end balancear a carga do tráfego de rede de entrada.

- Pool de endereços de back-end - configurará as interfaces de rede que receberão o tráfego com balanceamento de carga proveniente do pool de IPs de front-end.

- Regras de balanceamento de carga - configuração de porta local e de origem para o balanceador de carga.

- Investigações - configura a investigação de status de integridade para instâncias de Máquina Virtual.

- Regras NAT de entrada - configura as regras de porta para acessar diretamente uma das instâncias de Máquina Virtual.

É possível obter mais informações sobre componentes do balanceador de carga com o gerenciador de recursos do Azure em [Suporte do Gerenciador de Recursos do Azure para balanceador de carga](load-balancer-arm.md).

As etapas a seguir mostram como configurar um balanceador de carga entre 2 máquinas virtuais.


## Passo a passo usando o powershell


### Criar grupo de recursos para o balanceador de carga


### Etapa 1
Alterne para o modo PowerShell para usar os cmdlets do ARM. Há mais informações disponíveis em [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).


    PS C:\> Switch-AzureMode -Name AzureResourceManager

### Etapa 2

Faça logon na sua conta do Azure.


    PS C:\> Add-AzureAccount

Você deverá autenticar com suas credenciais.


### Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Para ver uma lista das assinaturas disponíveis, use o cmdlet “Get-AzureSubscription”.


### Etapa 4

Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

O Azure Resource Manager requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Certifique-se de que todos os comandos para criar um balanceador de carga usarão o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "NRP-RG" e o local "Oeste dos EUA".

## Crie a rede virtual e um endereço IP público para o pool de IP front-end


### Etapa 1

Criar uma rede virtual:

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Cria uma sub-rede para a rede virtual e atribui à $backendSubnet

	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Cria a rede virtual e adiciona a sub-rede lb-subnet-be à rede virtual NRPVNet.

### Etapa 2

Criar um endereço IP público a ser usada pelo pool de IP front-end:

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Dynamic -DomainNameLabel lbip

>[AZURE.NOTE]A propriedade do rótulo do nome de domínio do endereço IP público será o prefixo de FQDN do balanceador de carga.

## Criar o pool de IP front-end e pool de endereços de back-end

Configure um pool de IP front-end para o tráfego de rede do balanceador de carga de entrada e pool de endereços de back-end para receber o tráfego balanceado de carga.

### Etapa 1

Usando a variável IP pública ($publicIP), crie o pool de IP front-end.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP


### etapa 2:

Configure um pool de endereços de back-end usado para receber o tráfego de entrada de pool de IP front-end:

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## Criar regras de balanceamento de carga, regras de NAT, teste e balanceador de carga

Depois de criar o pool de IP front-end e o pool de endereços de back-end, você precisa criar as regras que pertencem o recurso de balanceador de carga:

### Etapa 1

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


O exemplo acima é a criação dos seguintes itens:

- Regra de NAT em que todo o tráfego de entrada para a porta 3441 irá para a porta 3389.
- uma segunda regra de NAT em que todo o tráfego de entrada para a porta 3442 irá para a porta 3389.
- uma regra que balanceará a carga de todo o tráfego de entrada na porta pública 80 para a porta local 80 no pool de endereços de back-end.
- uma regra de investigação que verificará o status de integridade para o caminho "HealthProbe.aspx"



### Etapa 2

Criar o balanceador de carga adicionando todos os objetos (regras de NAT, regras do balanceador de carga, configurações de teste) juntos:

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## Criar interfaces de rede

Depois de criar o balanceador de carga, você precisa definir quais interfaces de rede estarão recebendo o tráfego de rede com balanceamento de carga entrada, regras de NAT e teste. Nesse caso, a interface de rede é configurada individualmente e pode ser atribuída a uma máquina virtual posteriormente.


### Etapa 1


Obtenha a rede virtual do recurso e a sub-rede para criar interfaces de rede:

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


Nesta etapa, estamos criando uma interface de rede que pertencerá ao pool de back-end do balanceador de carga e associará a primeira regra NAT para RDP para essa interface de rede:

	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Etapa 2

Criar uma segunda chamada de interface de rede chamada LB-Nic2-BE:

Nesta etapa, estamos criando uma segunda interface de rede, atribuindo o mesmo pool de back-end do balanceador de carga e associando a segunda regra de NAT criada para RDP:

 	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


O resultado mostrará o seguinte:


PS C:\> $backendnic1


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



### Etapa 3

Use o comando Add-AzureVMNetworkInterface para atribuir o NIC a uma máquina virtual.

Você pode encontrar orientações de como criar uma máquina virtual e atribuir a ela um NIC de acordo com a documentação [Criar e pré-configurar uma máquina virtual do Windows com o Gerenciador de Recursos e o Azure PowerShell](../virtual-machines/virtual-machines-windows-create-powershell.md#Example), opção 4 ou 5.

## Atualizar um balanceador de carga existente


### Etapa 1

Usando o balanceador de carga do exemplo acima, atribua o objeto balanceador de carga à variável $slb usando Get-AzureLoadBalancer

	$slb=get-azureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

### Etapa 2

No exemplo a seguir, você adicionará uma nova regra de NAT de entrada usando a porta 81 no front-end e a porta 8181 do pool de back-end a um balanceador de carga existente

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Etapa 3

Salvar a nova configuração usando Set-AzureLoadBalancer

	$slb | Set-AzureLoadBalancer

## Remover um balanceador de carga

Use o comando Remove-AzureLoadBalancer para excluir um balanceador de carga criado anteriormente denominado "NRP-LB" em um grupo de recursos chamado " "NRP-RG"

	Remove-AzureLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Você pode usar a opção -Force para evitar a solicitação de exclusão.


## Veja também

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0427_2016-->