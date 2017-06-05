---
title: Abrir portas para uma VM usando o Azure PowerShell | Microsoft Docs
description: "Saiba como abrir uma porta/criar um ponto de extremidade para sua VM do Windows usando o modelo de implantação do Azure Resource Manager e o Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 0168bbc466f80c8603dda46ded56b7524e4e5e91
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Como abrir portas e pontos de extremidade para uma VM no Azure usando o PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Para criar um Grupo de Segurança de Rede e as regras de ACL, você precisa [ter a versão mais recente do Azure PowerShell instalada](/powershell/azureps-cmdlets-docs). Você também pode [executar essas etapas usando o Portal do Azure](nsg-quickstart-portal.md).

Faça logon na sua Conta do Azure:

```powershell
Login-AzureRmAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myNetworkSecurityGroup* e *myVnet*.

Crie uma regra com [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego *tcp* na porta *80*:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Em seguida, crie seu Grupo de Segurança de Rede com [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) e atribua a regra de HTTP que você acabou de criar conforme descrito a seguir. O exemplo a seguir cria um Grupo de Segurança de Rede chamado *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Agora, vamos atribuir seu Grupo de Segurança de Rede a uma sub-rede. O exemplo a seguir atribui uma rede virtual existente chamada *myVnet* à variável *$vnet* com [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell
$vnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associe seu Grupo de Segurança de Rede à sub-rede com [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig). O exemplo a seguir associa a sub-rede chamada *mySubnet* ao seu Grupo de Segurança de Rede:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Por fim, atualize sua rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) para que suas alterações entrem em vigor:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Você pode definir Grupos de Segurança de Rede e regras de ACL como parte dos modelos do Azure Resource Manager. Leia mais sobre a [criação de Grupos de Segurança de Rede com modelos](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar usar o encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua VM, use um balanceador de carga e regras de NAT (Conversão de Endereços de Rede). Por exemplo, talvez você queira expor a porta TCP 8080 externamente e direcionar o tráfego para a porta TCP 80 em uma VM. Você pode aprender sobre a [criação de um balanceador de carga para a Internet](../../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [O que é um NSG (grupo de segurança de rede)?](../../virtual-network/virtual-networks-nsg.md)
* [Visão Geral do Azure Resource Manager para Balanceadores de Carga](../../load-balancer/load-balancer-arm.md)


