---
title: Criar um balanceador de carga interno usando o PowerShell no Gerenciador de Recursos | Microsoft Docs
description: Saiba como criar um balanceador de carga interno no Gerenciador de Recursos usando o PowerShell
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: sewhee

---
# Introdução à criação de um balanceador de carga interno usando o PowerShell
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

<BR> [!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[modelo de implantação clássica](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

As etapas a seguir mostram como criar um balanceador de carga interno usando o gerenciador de recursos do Azure com o PowerShell. Com o Gerenciador de Recursos do Azure, os itens para criar um balanceador de carga interno são configurados individualmente e, em seguida, colocados juntos para criar um recurso.

Este artigo abordará a sequência de tarefas individuais que precisam ser realizadas para criar um balanceador de carga interno e explicará em detalhes o que é feito para alcançar a meta de criar um balanceador de carga.

## O que é necessário para criar um balanceador de carga interno?
Os itens a seguir devem ser configurados antes da criação de um balanceador de carga interno:

* Configuração do IP de front-end - vai configurar o endereço IP privado para tráfego de rede de entrada
* Pool de endereços de back-end - vai configurar as interfaces de rede que receberão o tráfego com balanceamento de carga proveniente do pool de IPs de front-end
* Regras de balanceamento de carga - configuração de porta local e de origem para o balanceador de carga.
* Investigações - configura a investigação de status de integridade para instâncias de Máquina Virtual.
* Regras NAT de entrada - configura as regras de porta para acessar diretamente uma das instâncias de Máquina Virtual.

É possível obter mais informações sobre componentes do balanceador de carga com o gerenciador de recursos do Azure em [Suporte do Gerenciador de Recursos do Azure para o balanceador de carga](load-balancer-arm.md).

As etapas a seguir mostram como configurar um balanceador de carga entre 2 máquinas virtuais.

## Passo a passo usando o Powershell
### Configurar o PowerShell para usar o Gerenciador de Recursos
Certifique-se de ter a versão de produção mais recente do módulo do Azure para PowerShell e de configurar corretamente o PowerShell para acessar sua assinatura do Azure.

### Etapa 1
        Login-AzureRmAccount



### Etapa 2
Verificar as assinaturas da conta

        Get-AzureRmSubscription 

Você deverá se autenticar com suas credenciais.<BR>

### Etapa 3
Escolha quais das suas assinaturas do Azure deseja usar.<BR>

        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Criar grupo de recursos para o balanceador de carga
### Etapa 4
Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. Certifique-se de que todos os comandos para criar um balanceador de carga usarão o mesmo grupo de recursos.

No exemplo anterior, criamos um grupo de recursos denominado "NRP-RG" e o local "Oeste dos EUA".

## Crie a Rede virtual e um endereço IP privado para o pool de IP front-end
### Etapa 1
Cria uma sub-rede para a rede virtual e atribui à variável $backendSubnet

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Criar uma rede virtual:

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Cria a rede virtual e adiciona a sub-rede lb-subnet-be à rede virtual NRPVNet e atribui à variável $vnet.

## Criar o pool de IP front-end e pool de endereços de back-end
Configure um pool de IP front-end para o tráfego de rede do balanceador de carga de entrada e pool de endereços de back-end para receber o tráfego balanceado de carga.

### Etapa 1
Crie um pool de IPs de front-end usando o endereço IP privado 10.0.2.5 para a sub-rede 10.0.2.0/24, que será o ponto de extremidade do tráfego de rede de entrada.

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### etapa 2:
Configure um pool de endereços de back-end usado para receber o tráfego de entrada de pool de IP front-end:

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## Criar regras de balanceamento de carga, regras de NAT, teste e balanceador de carga
Depois de criar o pool de IP front-end e o pool de endereços de back-end, você precisa criar as regras que pertencem o recurso de balanceador de carga:

### Etapa 1
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


O exemplo acima é a criação dos seguintes itens:

* Regra de NAT em que todo o tráfego de entrada para a porta 3441 irá para a porta 3389.
* uma segunda regra de NAT em que todo o tráfego de entrada para a porta 3442 irá para a porta 3389.
* uma regra que balanceará a carga de todo o tráfego de entrada na porta pública 80 para a porta local 80 no pool de endereços de back-end.
* uma regra de investigação que verificará o status de integridade para o caminho "HealthProbe.aspx"

### Etapa 2
Criar o balanceador de carga adicionando todos os objetos (regras de NAT, regras do balanceador de carga, configurações de teste) juntos:

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## Criar interfaces de rede
Depois de criar o balanceador de carga interno, você precisa definir quais interfaces de rede estarão recebendo o tráfego de rede com balanceamento de carga entrada, regras de NAT e teste. Nesse caso, a interface de rede é configurada individualmente e pode ser atribuída a uma máquina virtual posteriormente.

### Etapa 1
Obtenha a rede virtual do recurso e a sub-rede para criar interfaces de rede:

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


Nesta etapa, estamos criando uma interface de rede que pertencerá ao pool de back-end do balanceador de carga e associará a primeira regra NAT para RDP para essa interface de rede:

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Etapa 2
Criar uma segunda chamada de interface de rede chamada LB-Nic2-BE:

Nesta etapa, estamos criando uma segunda interface de rede, atribuindo o mesmo pool de back-end do balanceador de carga e associando a segunda regra de NAT criada para RDP:

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


O resultado mostrará o seguinte:

    $backendnic1

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



### Etapa 3
Use o comando Add-AzureRmVMNetworkInterface para atribuir a NIC a uma máquina virtual.

Você pode encontrar um passo a passo de como criar uma máquina virtual e atribuir a ela uma NIC seguindo a documentação [Criar e pré-configurar uma Máquina Virtual do Windows com o Gerenciador de Recursos e o Azure PowerShell](../virtual-machines/virtual-machines-windows-create-powershell.md#Example), opção 4 ou 5.

Ou se já tiver uma máquina virtual criada, você poderá adicionar a interface de rede com as etapas a seguir:

#### Etapa 1
Carregue o recurso de balanceador de carga em uma variável (se ainda não tiver feito isso). A variável usada é chamada de $lb e usa os mesmos nomes do recurso de balanceador de carga criado acima.

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### Etapa 2
Carregue a configuração de back-end em uma variável.

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### Etapa 3
Carregue a interface de rede já criada em uma variável. O nome da variável usada é $nic. O nome da interface de rede usada é o mesmo do exemplo acima.

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### Etapa 4
Altere a configuração de back-end na interface de rede.

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### Etapa 5
Salve o objeto de interface de rede.

    Set-AzureRmNetworkInterface -NetworkInterface $nic

Depois que uma interface de rede é adicionada ao pool de back-end do balanceador de carga, ela começa a receber tráfego de rede com base nas regras de balanceamento de carga para esse recurso de balanceador de carga.

## Atualizar um balanceador de carga existente
### Etapa 1
Usando o balanceador de carga do exemplo acima, atribua o objeto balanceador de carga à variável $slb usando Get-AzureRmLoadBalancer

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Etapa 2
No exemplo a seguir, você adicionará uma nova regra de NAT de entrada usando a porta 81 no front-end e a porta 8181 do pool de back-end a um balanceador de carga existente

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Etapa 3
Salvar a nova configuração usando Set-AzureLoadBalancer

    $slb | Set-AzureRmLoadBalancer

## Remover um balanceador de carga
Use o comando Remove-AzureRmLoadBalancer para excluir um balanceador de carga criado anteriormente denominado "NRP-LB" em um grupo de recursos chamado "NRP-RG"

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

> [!NOTE]
> Você pode usar a opção -Force para evitar a solicitação de exclusão.
> 
> 

## Próximas etapas
[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->