---
title: Criar VM do Windows usando o cmdlet simplificado New-AzureRMVM no Azure Cloud Shell | Microsoft Docs
description: "Aprenda rapidamente a criar máquinas virtuais Windows com o cmdlet simplificado New-AzureRMVM no Azure Cloud Shell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 94eb6232cf59d502a9d70545785c3788398f4d27
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Criar máquina virtual do Windows com o cmdlet simplificado New-AzureRMVM no Cloud Shell 

O cmdlet [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) adicionou um conjunto simplificado de parâmetros para criar uma nova VM usando o PowerShell. Este tópico mostra como usar o PowerShell no Azure Cloud Shell, com a versão mais recente do cmdlet New-AzureVM pré-instalado, para criar uma nova VM. Usaremos um conjunto de parâmetros simplificado que cria automaticamente todos os recursos necessários usando padrões inteligentes. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.1.1 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="create-the-vm"></a>Criar a VM

Use o cmdlet [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) para criar uma VM com padrões inteligentes que incluem o uso da imagem do Windows Server 2016 Datacenter a partir do Azure Marketplace. Use o New-AzureRMVM apenas com o parâmetro **-Name** e ele usará esse valor para todos os nomes de recursos. Neste exemplo, definimos o parâmetro **-Nome** como *myVM*. 

Verifique se **PowerShell** está selecionado no Cloud Shell e digite:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

Você será solicitado a criar um nome de usuário e uma senha para a VM, que será usada quando você se conectar à máquina virtual neste tópico. A senha deve ter de 12 a 123 caracteres e atender três dos quatro requisitos de complexidade a seguir: um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial.

Leva um minuto para criar a máquina virtual e os recursos associados. Quando terminar, você pode ver todos os recursos que foram criados usando o cmdlet [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Conectar-se à VM

Após a implantação, crie uma conexão de área de trabalho remota com a máquina virtual.

Utilize o comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para retornar o endereço IP público da máquina virtual. Anote o endereço IP público.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Em seu computador local, abra um prompt de comando e use o comando **mstsc** para iniciar uma sessão de área de trabalho remota com a nova VM. Substitua o endereço IP pelo &lt;publicIPAdress&gt; da sua máquina virtual. Quando solicitado, insira o nome de usuário e a senha fornecida para a VM quando ela foi criada.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Especificar nomes de recurso diferentes

Você também pode fornecer nomes mais descritivos para os recursos e ainda criá-los automaticamente. Veja um exemplo em que nomeamos vários recursos para a nova VM, incluindo um novo grupo de recursos.

```azurepowershell-interactive
New-AzureRmVm `
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

Quando não for mais necessário, você pode usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você implantou uma máquina virtual simples usando New-AzVM e, em seguida, conectado a ela via RDP. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Windows Azure](./tutorial-manage-vm.md)
