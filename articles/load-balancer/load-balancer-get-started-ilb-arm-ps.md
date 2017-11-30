---
title: Criar um balanceador de carga interno do Azure usando PowerShell | Microsoft Docs
description: "Saiba como criar um balanceador de carga interno usando o módulo do Azure PowerShell com o Azure Resource Manager"
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 6eea6c9bc7f686096c3cf0c97bfbe65a5507de2a
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Criar um balanceador de carga interno usando o módulo do Azure Powershell

> [!div class="op_single_selector"]
> * [Portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Introdução ao aplicativo à configuração

Este artigo explica como criar um balanceador de carga interno usando o Azure Resource Manager com o módulo do Azure PowerShell. No modelo de implantação do Gerenciador de Recursos, os objetos necessários para criar um balanceador de carga interno são configurados individualmente. Depois que os objetos são criados e configurados, eles são combinados para criar um balanceador de carga.

Para implantar um balanceador de carga, os seguintes objetos devem ser criados:

* Pool de IPs de front-end: O endereço IP privado para todo o tráfego de rede de entrada.
* Pool de endereços de back-end: As interfaces de rede para receber o tráfego de balanceamento de carga do endereço IP de front-end.
* Regras de balanceamento de carga: A configuração de porta (origem e local) para o balanceador de carga.
* Configuração de investigação: Investigações do status de integridade das máquinas virtuais.
* Regras NAT de entrada: As regras de porta para acesso direto às máquinas virtuais.

Para obter mais informações sobre os componentes do balanceador de carga, confira [Suporte do Azure Resource Manager para balanceador de carga](load-balancer-arm.md).

As etapas a seguir explicam como configurar um balanceador de carga entre duas máquinas virtuais.

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar o PowerShell para usar o Gerenciador de Recursos

Verifique se você tem a versão de produção mais recente do módulo do Azure PowerShell. O PowerShell deve ser configurado corretamente para acessar sua assinatura do Azure.

### <a name="step-1-start-powershell"></a>Etapa 1: Iniciar o PowerShell

Inicie o módulo do PowerShell para o Azure Resource Manager.

```powershell
Login-AzureRmAccount
```

### <a name="step-2-view-your-subscriptions"></a>Etapa 2: Exibir suas assinaturas

Verifique suas assinaturas do Azure disponíveis.

```powershell
Get-AzureRmSubscription
```

Quando você for solicitado a fornecer autenticação, insira suas credenciais.

### <a name="step-3-select-the-subscription-to-use"></a>Etapa 3: Selecione a assinatura a ser usada

Escolha qual das suas assinaturas do Azure você deve usar para implantar o balanceador de carga.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Etapa 4: Escolher o grupo de recursos para o balanceador de carga

Crie um novo grupo de recursos para o balanceador de carga. Ignore esta etapa se estiver usando um grupo de recursos existente.

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Esse local é usado como o padrão para todos os recursos do grupo de recursos em questão. Use sempre o mesmo grupo de recursos para todos os comandos relacionados à criação do balanceador de carga.

No exemplo anterior, criamos um grupo de recursos chamado **NRP-RG** com o local Oeste dos EUA.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Criar uma rede virtual e um endereço IP para o pool de IPs de front-end

Crie uma sub-rede para a rede virtual e atribua ela à variável **$backendSubnet**.

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Crie uma rede virtual.

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

A rede virtual foi criada. A sub-rede **LB-Subnet-BE** foi adicionada à rede virtual **NRPVNet**. Esses valores são atribuídos à variável **$vnet**.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Criar um pool de IPs de front-end e um pool de endereços de back-end

Crie um pool de IPs de front-end para o tráfego de entrada e um pool de endereços de back-end para receber o tráfego balanceado de carga.

### <a name="step-1-create-a-front-end-ip-pool"></a>Etapa 1: Criar um pool de IPs de front-end

Crie um pool de IPs de front-end com o endereço IP privado 10.0.2.5 para a sub-rede 10.0.2.0/24. Esse endereço é o ponto de extremidade de tráfego de rede de entrada.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Etapa 2: Criar um pool de endereços de back-end

Crie um pool de endereços de back-end para receber o tráfego de entrada do pool de IPs de front-end:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Criar as regras de configuração, investigação e balanceador de carga

Depois que o pool de IPs de front-end e o pool de endereços de back-end forem criados, especifique as regras para o recurso de balanceador de carga.

### <a name="step-1-create-the-configuration-rules"></a>Etapa 1: Criar as regras de configuração

O exemplo cria os quatro objetos de regra a seguir:

* Uma regra NAT de entrada para o protocolo RDP: Redireciona todo o tráfego de entrada na porta 3441 para a porta 3389.
* Uma segunda regra NAT de entrada para RDP: Redireciona todo o tráfego de entrada na porta 3442 para a porta 3389.
* Uma regra de investigação de integridade: Verifica o status de integridade do caminho HealthProbe.aspx.
* Uma regra do balanceador de carga: Faz o balanceamento de carga de todo o tráfego de entrada na porta pública 80 para a porta local 80 no pool de endereços de back-end.

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>Etapa 2: Criar o balanceador de carga

Crie o balanceador de carga e combine os objetos de regra (NAT de entrada para RDP, balanceador de carga e investigação de integridade):

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Criar as interfaces de rede

Depois de criar o balanceador de carga interno, defina quais interfaces de rede (NICs) receberão o tráfego de rede com balanceamento de carga entrada, regras de NAT e investigação. Cada interface de rede é configurada individualmente e é atribuída a uma máquina virtual posteriormente.

### <a name="step-1-create-the-first-network-interface"></a>Etapa 1: Criar a primeira rede virtual

Obtenha a rede virtual e a sub-rede do recurso. Esses valores são usados para criar as interfaces de rede:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Crie a primeira interface de rede com o nome **lb-nic1-be**. Atribua a interface o pool de back-end do balanceador de carga. Associe a primeira regra NAT para RDP com esta NIC:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Etapa 2: Criar a segunda rede virtual

Crie a segunda interface de rede com o nome **lb-nic2-be**. Atribua a segunda interface para o mesmo pool de back-end do balanceador de carga que a primeira interface. Associe a segunda NIC com a segunda regra NAT para RDP:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Examine a configuração:

    $backendnic1

As configurações devem ser as seguintes:

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
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
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



### <a name="step-3-assign-the-nic-to-a-vm"></a>Etapa 3: Atribuir a NIC a uma máquina virtual

Atribuia a NIC a uma máquina virtual usando o comando `Add-AzureRmVMNetworkInterface`.

Você pode encontrar as instruções passo a passo para criar uma máquina virtual e atribuir a NIC, confira [Criar uma VM do Azure usando o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Adicionar a interface de rede

Depois que a máquina virtual tiver sido criada, adicione a interface de rede.

### <a name="step-1-store-the-load-balancer-resource"></a>Etapa 1: Armazenar o recurso de balanceador de carga

Armazene o recurso de balanceador de carga em uma variável (se ainda não tiver feito isso). Estamos usando o nome da variável **$lb**. Para os valores de atributo no script, use os nomes para os recursos de balanceador de carga que foram criados nas etapas anteriores.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Etapa 2: Armazenar a configuração de back-end

Armazene a configuração de back-end para a variável **$backend**.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Etapa 3: Armazenar a interface de rede

Armazene a interface de rede em outra variável. Essa interface foi criada em "Criar interfaces de rede, na Etapa 1." Estamos usando o nome da variável **$nic1**. Use o mesmo nome de interface de rede do exemplo anterior.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Etapa 4: Alterar a configuração de back-end

Altere a configuração de back-end na interface de rede.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Etapa 5: Salve o objeto de interface de rede

Salve o objeto de interface de rede.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Depois que a interface é adicionada ao pool de back-end, o tráfego de rede tem a carga balanceada conforme as regras de balanceamento de carga. Essas regras foram configuradas em “Criar as regras de configuração, investigação e balanceador de carga”.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Etapa 1: Atribuir o objeto de balanceador de carga a uma variável

Atribua o objeto de balanceador de carga (do exemplo anterior) à variável **$slb** usando o comando `Get-AzureRmLoadBalancer`:

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Etapa 2: Adicionar uma regra NAT

Adicione uma nova regra NAT de entrada para um balanceador de carga. Use a porta 81 para o pool de front-end e a porta 8181 para o pool de back-end:

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Etapa 3: Salve a configuração

Salve a nova configuração usando o comando `Set-AzureLoadBalancer`:

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Remover um balanceador de carga existente

Exclua o balanceador de carga **NRP-LB** no grupo de recursos **NRP-RG** usando o comando `Remove-AzureRmLoadBalancer`:

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Use **-Force** opcional para impedir o prompt de confirmação para a exclusão.

## <a name="next-steps"></a>Próximas etapas

* [Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)
* [Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
