---
title: Criar VM do Windows usando o cmdlet simplificado New-AzVM no Azure Cloud Shell | Microsoft Docs
description: Aprenda rapidamente a criar máquinas virtuais Windows com o cmdlet simplificado New-AzVM no Azure Cloud Shell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 02f2bd78ca5656534b106c6f7f18c05165b4b9ff
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444495"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>Criar máquina virtual do Windows com o cmdlet simplificado New-AzVM no Cloud Shell 

O cmdlet [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) adicionou um conjunto simplificado de parâmetros para criar uma nova VM usando o PowerShell. Este tópico mostra como usar o PowerShell no Azure Cloud Shell, com a versão mais recente do cmdlet New-AzureVM pré-instalado, para criar uma nova VM. Usaremos um conjunto de parâmetros simplificado que cria automaticamente todos os recursos necessários usando padrões inteligentes. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]


## <a name="create-the-vm"></a>Criar a VM

Você pode usar o [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) para criar uma VM com padrões inteligentes que incluam usando a imagem do Windows Server 2016 Datacenter do Azure Marketplace. Use o New-AzVM apenas com o parâmetro **-Name** e ele usará esse valor para todos os nomes de recursos. Neste exemplo, definimos o parâmetro **-Nome** como *myVM*. 

Verifique se **PowerShell** está selecionado no Cloud Shell e digite:

```azurepowershell-interactive
New-AzVm -Name myVM
```

Você será solicitado a criar um nome de usuário e uma senha para a VM, que será usada quando você se conectar à máquina virtual neste tópico. A senha deve ter de 12 a 123 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial.

Leva um minuto para criar a máquina virtual e os recursos associados. Quando terminar, você pode ver todos os recursos criados usando o cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource).

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Conectar-se à VM

Após a implantação, crie uma conexão de área de trabalho remota com a máquina virtual.

Utilize o comando [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público da máquina virtual. Anote o endereço IP público.

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Em seu computador local, abra um prompt de comando e use o comando **mstsc** para iniciar uma sessão de área de trabalho remota com a nova VM. Substitua o endereço IP pelo &lt;publicIPAdress&gt; da sua máquina virtual. Quando solicitado, insira o nome de usuário e a senha fornecida para a VM quando ela foi criada.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Especificar nomes de recurso diferentes

Você também pode fornecer nomes mais descritivos para os recursos e ainda criá-los automaticamente. Veja um exemplo em que nomeamos vários recursos para a nova VM, incluindo um novo grupo de recursos.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você implantou uma máquina virtual simples usando New-AzVM e, em seguida, conectado a ela via RDP. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](./tutorial-manage-vm.md)
