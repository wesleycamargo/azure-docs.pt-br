---
title: Tutorial – Instalar aplicativos em uma VM Windows no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar a Extensão de Script Personalizado para executar scripts e implantar aplicativos em máquinas virtuais do Windows no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 605649992cad988b2630034c7fdb8df1dc0fd5f5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435387"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Tutorial – Implantar aplicativos em uma máquina virtual do Windows no Azure com a Extensão de Script Personalizado

Para configurar VMs (máquinas virtuais) de uma maneira rápida e consistente, alguma forma de automação é em geral desejada. Uma abordagem comum para personalizar uma VM do Windows é usar [Extensão de script personalizado para o Windows](extensions-customscript.md). Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Usar a Extensão de Script Personalizado para instalar o IIS
> * Criar uma VM que usa a Extensão de Script Personalizado
> * Exibir um site do IIS em execução depois que a extensão é aplicada

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.


## <a name="custom-script-extension-overview"></a>Visão geral da extensão de script personalizado
A extensão de script personalizado baixa e executa scripts em VMs do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão.

A extensão de script personalizado se integra com modelos do Azure Resource Manager e também pode ser executada usando a CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure.

Usar a Extensão de script personalizado com VMs do Linux e Windows.


## <a name="create-virtual-machine"></a>Criar máquina virtual
Primeiro, defina o nome de usuário e a senha de um administrador para a VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora você pode criar uma VM com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo a seguir cria uma VM chamada *myVM* na localização *EastUs*. Se ainda não existirem, o grupo de recursos *myResourceGroupAutomate* e recursos de rede de suporte são criados. Para permitir o tráfego da web, o cmdlet também abre a porta *80*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Demora alguns minutos para que os recursos e a VM sejam criados.


## <a name="automate-iis-install"></a>Automatizar a instalação do IIS
Use [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para instalar a extensão de script personalizado. A extensão executa `powershell Add-WindowsFeature Web-Server` para instalar o servidor Web do IIS e, em seguida, atualiza a página *Default.htm* para mostrar o nome do host da VM:

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testar o site
Obtenha o endereço IP público do balanceador de carga com [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). O exemplo a seguir obtém o endereço IP para *myPublicIPAddress* criado anteriormente:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Você pode inserir o endereço IP público em um navegador da Web. O site é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego como no exemplo a seguir:

![Site do IIS em execução](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você automatizou a instalação do IIS em uma VM. Você aprendeu como:

> [!div class="checklist"]
> * Usar a Extensão de Script Personalizado para instalar o IIS
> * Criar uma VM que usa a Extensão de Script Personalizado
> * Exibir um site do IIS em execução depois que a extensão é aplicada

Vá para o próximo tutorial para aprender a gerenciar imagens de VM.

> [!div class="nextstepaction"]
> [Criar imagens de VM personalizada](./tutorial-custom-images.md)
