---
title: Comandos comuns do PowerShell para VMs | Microsoft Docs
description: "Comandos comuns do PowerShell para você começar a criar e gerenciar suas VMs no Azure no Windows"
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
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 39739902815dacd40d4d57d503344217d4fa112d


---
# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Comandos comuns do PowerShell para criação e gerenciamento de VMs
Este artigo aborda alguns dos comandos do Azure PowerShell que você pode usar para criar e gerenciar máquinas virtuais em sua assinatura do Azure.  Para obter uma ajuda mais detalhada com opções específicas de linha de comando, você pode usar o *comando* **Get-Help**.

Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

## <a name="create-a-vm"></a>Criar uma máquina virtual
| Tarefa | Get-Help |
| --- | --- |
| Criar uma configuração de VM |$vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "vm_name" -VMSize "vm_size"<BR></BR><BR></BR>A configuração da VM é usada para definir ou atualizar as configurações da VM. A configuração é inicializada com o nome da VM e seu [tamanho](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adicionar definições de configuração |$vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "computer_name" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>As configurações do sistema operacional, incluindo as [credenciais](https://technet.microsoft.com/library/hh849815.aspx), são adicionadas ao objeto de configuração que você criou anteriormente usando New-AzureRmVMConfig. |
| Adicionar uma interface de rede |$vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Uma VM deve ter um [adaptador de rede](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para se comunicar em uma rede virtual. Você também pode usar [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) para recuperar um objeto de interface de rede existente. |
| Especificar uma imagem de plataforma |$vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>As [informações da imagem](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) são adicionadas ao objeto de configuração que você criou anteriormente usando New-AzureRmVMConfig. O objeto retornado por este comando só é usado quando você configura o disco do sistema operacional para usar uma imagem de plataforma. |
| Definir o disco do sistema operacional para usar uma imagem de plataforma |$vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "disk_name" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage<BR></BR><BR></BR>O nome do disco do sistema operacional e seu local no [armazenamento](../storage/storage-powershell-guide-full.md) são adicionados ao objeto de configuração que você criou anteriormente. |
| Definir o disco do sistema operacional para usar uma imagem generalizada |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "disk_name" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>O nome do disco do sistema operacional, o local da imagem de origem e o local do disco no [armazenamento](../storage/storage-powershell-guide-full.md) são adicionados ao objeto de configuração. |
| Definir o disco do sistema operacional para usar uma imagem especializada |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "name_of_disk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Criar uma máquina virtual |[New-AzureRmVM]() -ResourceGroupName "resource_group_name" -Location "location_name" -VM $vm<BR></BR><BR></BR>Todos os recursos são criados em um [grupo de recursos](../powershell-azure-resource-manager.md). A VM deve ser criada no cache no mesmo [local](https://msdn.microsoft.com/library/azure/dn495177.aspx) que o grupo de recursos. Antes de executar esse comando, execute New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface e Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Obter informações sobre VMs
| Tarefa | Command |
| --- | --- |
| Listar VMs em uma assinatura |[Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx) |
| Listar VMs em um grupo de recursos |Get-AzureRmVM -ResourceGroupName "resource_group_name"<BR></BR><BR></BR>Para obter uma lista de grupos de recursos em sua assinatura, use [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx). |
| Obter informações sobre uma VM |Get-AzureRmVM -ResourceGroupName "resource_group_name" -Name "vm_name" |

## <a name="manage-vms"></a>Gerenciar VMs
| Tarefa | Command |
| --- | --- |
| Iniciar uma VM |[Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| Parar uma VM |[Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| Reiniciar uma VM em execução |[Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| Excluir uma VM |[Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "resource_group_name" -Name "vm_name" |
| Generalizar uma VM |[Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "vm_name" -Generalized<BR></BR><BR></BR>Execute esse comando antes de executar Save-AzureRmVMImage. |
| Capturar uma VM |[Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "resource_group_name" -VMName "vm_name" -DestinationContainerName "image_container" -VHDNamePrefix "image_name_prefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>Uma máquina virtual deve ser [desligada e generalizada](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ser usada na criação de uma imagem. Antes de executar esse comando, execute Set-AzureRmVm. |
| Atualizar uma VM |[Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "resource_group_name" -VM $vm<BR></BR><BR></BR>Obtenha a configuração atual da VM usando Get-AzureRmVM, altere as configurações no objeto da VM e execute este comando. |
| Adicionar um disco de dados a uma VM |[Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "disk_name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Usar Get-AzureRmVM para obter o objeto da VM. Especifique o número LUN e o tamanho do disco. Execute Update-AzureRmVM para aplicar as alterações de configuração da VM. O disco que você adiciona não é inicializado. Para obter informações sobre como inicializar discos à medida que eles são adicionados, veja [Gerenciar Máquinas Virtuais do Azure usando o Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Remover um disco de dados de uma VM |[Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "disk_name"<BR></BR><BR></BR>Usar Get-AzureRmVM para obter o objeto da VM. Execute Update-AzureRmVM para aplicar as alterações de configuração da VM. |
| Adicionar uma extensão a uma VM |[Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "resource_group_name" -Location "azure_location" -VMName "vm_name" -Name "extension_name" -Publisher "publisher_name" -Type "extension_type" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Execute esse comando com as [informações de configuração](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) apropriadas para a extensão que deseja instalar. |
| Remover uma extensão de VM |[Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "resource_group_name" -Name "extension_name" -VMName "vm_name" |

## <a name="next-steps"></a>Próximas etapas
* Veja as etapas básicas para criar uma máquina virtual em [Criar uma VM do Windows utilizando o Resource Manager e o PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


