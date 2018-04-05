---
title: Criar um Load Balancer Standard público com frontend de endereço IP público com redundância de zona usando o PowerShell | Microsoft Docs
description: Saiba como criar um Load Balancer Standard público com um frontend de endereço IP público com redundância de zona usando o PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 2849ef9b862b4471f348ac83a4a18eb9a3a4d42a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>Criar um Load Balancer Standard público com frontend de endereço IP Público com redundância de zona usando o PowerShell

Este artigo segue as etapas para a criação de um [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) público com um front-end com redundância de zona usando um endereço IP Público Standard.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="register-for-availability-zones-preview"></a>Registrar para a Versão Prévia das Zonas de Disponibilidade

Este artigo requer que você tenha a versão 4.4.0 ou superior do módulo AzureRM instalada. Para saber qual é a versão, execute `Get-Module -ListAvailable AzureRM`. Se você precisar instalar ou atualizar, instale a versão mais recente do módulo AzureRM por meio da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!NOTE]
> As zonas de disponibilidade estão em versão prévia e prontas para seus cenários de desenvolvimento e teste. O suporte está disponível para famílias de tamanhos de VM, regiões e recursos do Azure selecionados. Para obter mais informações sobre como começar e com quais recursos, regiões e famílias de tamanhos de VM do Azure você pode experimentar as zonas de disponibilidade, confira [Visão geral das Zonas de Disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Para obter suporte, entre em contato em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [abra um tíquete de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um Grupo de Recursos usando o seguinte comando:

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Criar um IP público Standard 
Crie um IP Público Standard usando o seguinte comando:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Criar uma configuração de IP de front-end para o site

Crie uma configuração de IP de front-end usando o seguinte comando:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Criar o pool de endereços de back-end

Crie um pool de endereços de back-end usando o seguinte comando:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Criar uma investigação do balanceador de carga na porta 80

Crie uma investigação de integridade para o balanceador de carga na porta 80 usando o seguinte comando:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga
 Crie uma regra do balanceador de carga usando o seguinte comando:

```powershell
   $rule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Crie uma investigação de integridade do Load Balancer Standard usando o seguinte comando:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Próximas etapas
- Saiba como [criar um IP público em uma zona de disponibilidade](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



