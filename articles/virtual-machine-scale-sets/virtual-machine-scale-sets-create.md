---
title: "Criar um Conjunto de Dimensionamento de Máquinas Virtuais do Azure | Microsoft Docs"
description: "Crie e implante um Conjunto de Dimensionamento de Máquinas Virtuais do Azure Linux ou Windows com a CLI do Azure, o PowerShell, um modelo ou o Visual Studio."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 949e59b64557ac3efc07da73a205c808e25aeaab
ms.lasthandoff: 03/22/2017

---

# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>Criar e implantar um Conjunto de Dimensionamento de Máquinas Virtuais
Os conjuntos de escala de máquina virtual facilitam a implantação e o gerenciamento de máquinas virtuais idênticas como um conjunto. Os conjuntos de dimensionamento fornecem uma camada de computação altamente escalonável e personalizável para aplicativos de hiperescala e suporte a imagens da plataforma Windows, imagens da plataforma Linux, imagens personalizadas e extensões. Para obter mais informações sobre conjuntos de dimensionamento, confira [Conjuntos de Dimensionamento de Máquina Virtual](virtual-machine-scale-sets-overview.md).

Este tutorial mostra como criar um conjunto de dimensionamento de máquinas virtuais **sem** usar o portal do Azure. Para saber mais sobre como usar o portal do Azure, confira [Como criar um Conjunto de Dimensionamento de Máquinas Virtuais usando o portal do Azure](virtual-machine-scale-sets-portal-create.md).

>[!NOTE]
>Para saber mais sobre os recursos do Azure Resource Manager, confira [Azure Resource Manager vs. Implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Se estiver usando a CLI 2.0 do Azure ou o PowerShell para criar um conjunto de dimensionamento, primeiramente, você precisa fazer logon na sua assinatura.

Para saber mais sobre como instalar, configurar e fazer logon no Azure com a CLI 2.0 do Azure ou o PowerShell, confira [Getting Started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) (Introdução à CLI 2.0 do Azure) ou [Get started with Azure PowerShell cmdlets](/powershell/resourcemanager/) (Introdução aos cmdlets do Azure PowerShell).

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="prep-create-a-resource-group"></a>Preparação: Criar um grupo de recursos

Primeiro, você precisa criar um grupo de recursos ao qual o conjunto de dimensionamento de máquinas virtuais é associado.

```azurecli
az group create --location westus2 --name vmss-test-1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name vmss-test-1
```

## <a name="create-from-azure-cli"></a>Criar com a CLI do Azure

Com a CLI do Azure, você pode criar um conjunto de dimensionamento de máquinas virtuais com esforço mínimo. Os valores padrão serão fornecidos se você omiti-los. Por exemplo, se você não especificar informações de rede virtual, ela será criada para você. Se omitidas, as seguintes partes serão criadas para você: um balanceador de carga, uma rede virtual e um endereço IP público.

Ao escolher a imagem de máquina virtual que quer usar no conjunto de dimensionamento de máquinas virtuais, você tem algumas opções:

1. URN  
O identificador de um recurso:  
**Win2012R2Datacenter**.

2. Alias do URN  
O nome amigável de um URN:  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**.

3. ID de recurso personalizado  
O caminho para um recurso do Azure:  
**/subscriptions/subscription-guid/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**.

4. Recurso da Web  
O caminho para um URI do HTTP:  
**http://contoso.blob.core.windows.net/vhds/osdiskimage.vhd**.

>[!TIP]
>Você pode encontrar a lista de imagens disponíveis com `az vm image list`.

Para criar um conjunto de dimensionamento de máquinas virtuais, você deve especificar o _grupo de recursos_, o _nome_, a _imagem do sistema operacional_ e as _informações de autenticação_. O exemplo a seguir cria um conjunto de dimensionamento de máquinas virtuais (essa etapa pode levar alguns minutos).

```azurecli
az vmss create --resource-group vmss-test-1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

## <a name="create-from-powershell"></a>Criar com o PowerShell

O PowerShell é mais complicado de usar do que a CLI do Azure. Enquanto a CLI do Azure fornece padrões para recursos relacionados à rede (balanceador de carga, endereço ip, uma rede virtual), o PowerShell, não. Fazer referência a uma imagem também é um pouco mais complicado. Você pode obter imagens com os seguintes cmdlets:

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

O trabalho dos cmdlets pode ser colocado em sequência. Veja um exemplo de como obter todas as imagens para a região **Oeste dos EUA 2**, cujo editor tem o nome **microsoft** nele.

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

O fluxo de trabalho para criar um conjunto de dimensionamento de máquinas virtuais é:

1. Criar um objeto de configuração que retenha informações sobre o conjunto de dimensionamento.
2. Fazer referência à imagem do SO base.
3. Definir as configurações do sistema operacional: autenticação, prefixo de nome da VM, usuário/frase secreta.
4. Configurar a rede.
5. Criar o conjunto de dimensionamento.

Este exemplo cria um conjunto de dimensionamento básico de 2 instâncias com o Windows Server 2016 instalado.

```powershell
# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location WestUS2 -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Setup information about how to authenticate with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName "vmss-test-1" -Location "westus2" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ip-address" -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step may take a few minutes)
New-AzureRmVmss -ResourceGroupName vmss-test-1 -Name my-scale-set -VirtualMachineScaleSet $vmssConfig
```

## <a name="create-from-a-template"></a>Criar usando um modelo

Você pode implantar um conjunto de dimensionamento de máquinas virtuais usando um Modelo do Azure Resource Manager. É possível criar seu próprio modelo ou usar um do [repositório de modelos](https://azure.microsoft.com/resources/templates/?term=vmss). Esses modelos podem ser implantados diretamente na sua assinatura do Azure.

>[!NOTE]
>Para criar seu próprio modelo, crie um arquivo de texto _.json_. Para obter informações gerais sobre como criar e personalizar um modelo, confira [Modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Um modelo de exemplo está disponível [no GitHub](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set). Para saber mais sobre como criar e usar esse exemplo, confira [Conjunto de dimensionamento mínimo viável](.\virtual-machine-scale-sets-mvss-start.md).

## <a name="create-from-visual-studio"></a>Criar com o Visual Studio

Com o Visual Studio, você pode criar um projeto do Grupo de Recursos do Azure e adicionar a ele um modelo do Conjunto de Dimensionamento de Máquinas Virtuais. É possível escolher qual modelo deseja importar; por exemplo, do GitHub ou da Galeria do Azure. Um script de implantação do PowerShell também é gerado para você. Para saber mais, confira [Como criar um Conjunto de Dimensionamento de Máquinas Virtuais com o Visual Studio](virtual-machine-scale-sets-vs-create.md).

## <a name="create-from-the-azure-portal"></a>Criar com o portal do Azure

O portal do Azure é uma maneira conveniente de criar rapidamente um conjunto de dimensionamento. Para saber mais, confira [Como criar um Conjunto de Dimensionamento de Máquinas Virtuais usando o portal do Azure](virtual-machine-scale-sets-portal-create.md).

