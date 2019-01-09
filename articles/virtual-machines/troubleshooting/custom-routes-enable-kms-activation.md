---
title: Usar as rotas personalizadas do Azure para habilitar a ativação KMS com túnel forçado | Microsoft Docs
description: Mostra como usar as rotas personalizadas do Azure para habilitar a ativação KMS com túnel forçado no Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: f1e2ab6a954361a7807d78dc2baf5d24af52a679
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797262"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Falha de ativação do Windows no cenário de túnel forçado

Este artigo descreve como resolver o problema de ativação do KMS que pode ocorrer quando você tiver habilitado o túnel em cenários de ExpressRoute ou conexão de VPN site a site forçado.

## <a name="symptom"></a>Sintoma

Você habilita [túnel forçado](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) nas sub-redes virtuais do Azure para direcionar todo o tráfego de limite de internet de volta para a sua rede local. Nesse cenário, as máquinas virtuais do Azure (VM) que executam o Windows Server 2012 R2 ou versões posteriores com êxito podem ativar o Windows. No entanto, as VMs que executam a versão anterior do Windows não ativam o Windows. 

## <a name="cause"></a>Causa

As VMs do Windows Azure precisarão se conectar ao servidor KMS do Azure para ativação do Windows. A ativação requer que a solicitação de ativação venha de um endereço IP público do Azure. No cenário de túnel forçado, a ativação falhará porque a solicitação de ativação é de sua rede local, em vez de um IP público do Azure. 

## <a name="solution"></a>Solução

Para resolver esse problema, use a rota personalizada para o tráfego de ativação de rota para o servidor AKMS do Azure (23.102.135.246). 

O endereço IP 23.102.135.246 é o endereço IP do servidor KMS para a nuvem Global do Azure. Seu nome DNS é kms.core.windows.net. Se você usar outras plataformas do Azure, como Azure Alemanha, você deve usar o endereço IP do servidor do KMS correspondente. Para obter mais informações, confira a tabela a seguir:

|Plataforma| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Alemanha|kms.core.cloudapi.de|51.4.143.248|
|Governo dos EUA para Azure|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Para adicionar a rota personalizada, siga essas etapas:

### <a name="for-resource-manager-vms"></a>VMs do Resource Manager

1. Abra o Azure PowerShell e [conecte-se à sua conta do Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Execute os seguintes comandos:

    ```powershell
    # First, we will get the virtual network hosts the VMs that has activation problems. In this case, I get virtual network ArmVNet-DM in Resource Group ArmVNet-DM

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, we create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out

    $RouteTable = New-AzureRmRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzureRmRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzureRmRouteTable -RouteTable $RouteTable
    ```
3. Vá para a VM que tem o problema de ativação, use [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) para testar se ele puder acessar o servidor KMS:

        psping kms.core.windows.net:1688

4. Tente se conectar à VM e veja se o problema foi resolvido.

### <a name="for-classic-vms"></a>Para VMs Clássicas

1. Abra o Azure PowerShell e [conecte-se à sua conta do Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Execute os seguintes comandos:

    ```powershell
    # First, we will create a new route table
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the routetable that was created
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply KMS route table to the subnet that host the problem VMs (in this case, I will apply it to the subnet named Subnet-1)
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Vá para a VM que tem o problema de ativação, use [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) para testar se ele puder acessar o servidor KMS:

        psping kms.core.windows.net:1688

4. Tente se conectar à VM e veja se o problema foi resolvido.

## <a name="next-steps"></a>Próximas etapas

- [Chaves de instalação de cliente KMS](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Revisar e Selecionar Métodos de Ativação](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)