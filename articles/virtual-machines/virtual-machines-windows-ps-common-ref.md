---
title: "Comandos comuns do PowerShell para Máquinas Virtuais do Azure | Microsoft Docs"
description: "Comandos comuns do PowerShell para você começar a criar e gerenciar as VMs Windows no Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 845f5f0df75d1e17b950e06a2fa480fdc69b652b
ms.lasthandoff: 03/03/2017


---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandos comuns do PowerShell para criar e gerenciar Máquinas Virtuais do Azure

Este artigo aborda alguns dos comandos do Azure PowerShell que você pode usar para criar e gerenciar máquinas virtuais em sua assinatura do Azure.  Para obter uma ajuda mais detalhada com opções específicas de linha de comando, você pode usar o *comando* **Get-Help**.

Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

Essas variáveis poderão ser úteis para você se estiver executando mais de um dos comandos descritos neste artigo:

- $location – o local da máquina virtual. É possível usar [Get-AzureRmLocation](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/get-azurermlocation) para encontrar uma [região geográfica](https://azure.microsoft.com/regions/) que funciona para você.
- $myResourceGroup – o nome do grupo de recursos que contém a máquina virtual.
- $myVM – o nome da máquina virtual.

## <a name="create-a-vm"></a>Criar uma máquina virtual

| Tarefa | Get-Help |
| ---- | ------- |
| Criar uma configuração de VM |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/New-AzureRmVMConfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>A configuração da VM é usada para definir ou atualizar as configurações da VM. A configuração é inicializada com o nome da VM e seu [tamanho](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adicionar definições de configuração |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Set-AzureRmVMOperatingSystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>As configurações do sistema operacional, incluindo as [credenciais](https://technet.microsoft.com/library/hh849815.aspx), são adicionadas ao objeto de configuração que você criou anteriormente usando New-AzureRmVMConfig. |
| Adicionar uma interface de rede |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Uma VM deve ter um [adaptador de rede](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para se comunicar em uma rede virtual. Você também pode usar [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v3.4.0/Get-AzureRmNetworkInterface) para recuperar um objeto de interface de rede existente. |
| Especificar uma imagem de plataforma |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Set-AzureRmVMSourceImage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>As [informações da imagem](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) são adicionadas ao objeto de configuração que você criou anteriormente usando New-AzureRmVMConfig. O objeto retornado por este comando só é usado quando você configura o disco do sistema operacional para usar uma imagem de plataforma. |
| Definir o disco do sistema operacional para usar uma imagem de plataforma |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Set-AzureRmVMOSDisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>O nome do disco do sistema operacional e seu local no [armazenamento](../storage/storage-powershell-guide-full.md) são adicionados ao objeto de configuração que você criou anteriormente. |
| Definir o disco do sistema operacional para usar uma imagem generalizada |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>O nome do disco do sistema operacional, o local da imagem de origem e o local do disco no [armazenamento](../storage/storage-powershell-guide-full.md) são adicionados ao objeto de configuração. |
| Definir o disco do sistema operacional para usar uma imagem especializada |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Criar uma máquina virtual |[New-AzureRmVM]() -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Todos os recursos são criados em um [grupo de recursos](../powershell-azure-resource-manager.md). Antes de executar esse comando, execute New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface e Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Obter informações sobre VMs

| Tarefa | Command |
| ---- | ------- |
| Listar VMs em uma assinatura |[Get-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Get-AzureRmVM) |
| Listar VMs em um grupo de recursos |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Para obter uma lista de grupos de recursos em sua assinatura, use [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/get-azurermresourcegroup). |
| Obter informações sobre uma VM |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Gerenciar VMs
| Tarefa | Command |
| --- | --- |
| Iniciar uma VM |[Start-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Start-AzureRmVM) -ResourceGroupName $myResourceGroup -Name $myVM |
| Parar uma VM |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Stop-AzureRmVM) -ResourceGroupName $myResourceGroup -Name $myVM |
| Reiniciar uma VM em execução |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Restart-AzureRmVM) -ResourceGroupName $myResourceGroup -Name $myVM |
| Excluir uma VM |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Remove-AzureRmVM) -ResourceGroupName $myResourceGroup -Name $myVM |
| Generalizar uma VM |[Set-AzureRmVm](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Set-AzureRmVM) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>Execute esse comando antes de executar Save-AzureRmVMImage. |
| Capturar uma VM |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Save-AzureRmVMImage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>Uma máquina virtual deve ser [desligada e generalizada](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ser usada na criação de uma imagem. Antes de executar esse comando, execute Set-AzureRmVm. |
| Atualizar uma VM |[Update-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Update-AzureRmVM) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Obtenha a configuração atual da VM usando Get-AzureRmVM, altere as configurações no objeto da VM e execute este comando. |
| Adicionar um disco de dados a uma VM |[Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMDataDisk) -VM $vm -Name "myDataDisk" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Usar Get-AzureRmVM para obter o objeto da VM. Especifique o número LUN e o tamanho do disco. Execute Update-AzureRmVM para aplicar as alterações de configuração da VM. O disco que você adiciona não é inicializado. Para obter informações sobre como inicializar discos à medida que eles são adicionados, veja [Gerenciar Máquinas Virtuais do Azure usando o Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Remover um disco de dados de uma VM |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Remove-AzureRmVMDataDisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Usar Get-AzureRmVM para obter o objeto da VM. Execute Update-AzureRmVM para aplicar as alterações de configuração da VM. |
| Adicionar uma extensão a uma VM |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Set-AzureRmVMExtension) -ResourceGroupName $myResourceGroup -Location $location -VMName $myVM -Name "extensionName" -Publisher "publisherName" -Type "extensionType" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Execute esse comando com as [informações de configuração](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) apropriadas para a extensão que deseja instalar. |
| Remover uma extensão de VM |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Remove-AzureRmVMExtension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>Próximas etapas
* Veja as etapas básicas para criar uma máquina virtual em [Criar uma VM do Windows utilizando o Resource Manager e o PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


