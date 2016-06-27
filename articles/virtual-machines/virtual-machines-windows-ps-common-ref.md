<properties 
   pageTitle="Comandos comuns do PowerShell para VMs | Microsoft Azure"
   description="Comandos comuns do PowerShell para você começar a criar e gerenciar suas VMs no Azure no Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="davidmu" />

# Comandos comuns do PowerShell para criação e gerenciamento de VMs

Este artigo aborda alguns dos comandos do Azure PowerShell que você pode usar para criar e gerenciar máquinas virtuais em sua assinatura do Azure. Para obter uma ajuda mais detalhada com opções específicas de linha de comando, você pode usar o *comando* **Get-Help**.

## Criar recursos usando o Azure PowerShell

Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura que você deseja usar e entrar na sua conta do Azure.

Tarefa | Command
-------------- | -------------------------
Criar uma configuração de VM | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "vm\_name" -VMSize "vm\_size"<BR></BR><BR></BR>A configuração da VM é usada para definir ou atualizar as configurações da VM. A configuração é inicializada com o nome da VM e seu [tamanho](virtual-machines-windows-sizes.md).
Adicionar definições de configuração | $vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "computer\_name" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Configurações do sistema operacional incluindo [credenciais](https://technet.microsoft.com/library/hh849815.aspx) são adicionadas ao objeto de configuração que você criou anteriormente usando New-AzureRmVMConfig.
Adicionar uma interface de rede | $vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Uma VM precisa ter uma [interface de rede](virtual-machines-windows-ps-create.md) para se comunicar em uma rede virtual. Você também pode usar [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) para recuperar um objeto de interface de rede existente.
Especificar uma imagem de plataforma | $vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "publisher\_name" -Offer "publisher\_offer" -Skus "product\_sku" -Version "latest"<BR></BR><BR></BR>[Informações da imagem](virtual-machines-windows-cli-ps-findimage.md) são adicionadas ao objeto de configuração que você criou anteriormente usando New-AzureRmVMConfig. O objeto retornado por este comando só é usado quando você configura o disco do sistema operacional para usar uma imagem de plataforma.
Definir o disco do sistema operacional para usar uma imagem de plataforma | $vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "disk\_name" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption fromImage<BR></BR><BR></BR>O nome do disco do sistema operacional e onde ele ficará no [armazenamento](../storage/storage-powershell-guide-full.md) é adicionado ao objeto de configuração que você criou anteriormente.
Definir o disco do sistema operacional para usar uma imagem generalizada | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "disk\_name" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption fromImage -Windows<BR></BR><BR></BR>O nome do disco do sistema operacional, o local da imagem de origem e onde o disco ficará no [armazenamento](../storage/storage-powershell-guide-full.md) é adicionado ao objeto de configuração criado anteriormente.
Definir o disco do sistema operacional para usar uma imagem especializada | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "name\_of\_disk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows
Criar uma máquina virtual | [New-AzureRmVM]() -ResourceGroupName "resource\_group\_name" -Location "location\_name" -VM $vm<BR></BR><BR></BR>Todos os recursos são criados em um [grupo de recursos](../powershell-azure-resource-manager.md). A VM deve ser criada no cache no mesmo [local](https://msdn.microsoft.com/library/azure/dn495177.aspx) que o grupo de recursos. Antes de executar esse comando, execute New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface e Set-AzureRmVMOSDisk.
Listar VMs em uma assinatura| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Listar VMs em um grupo de recursos | Get-AzureRmVM -ResourceGroupName "resource\_group\_name"<BR></BR><BR></BR>Para obter uma lista de grupos de recursos em sua assinatura, use [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Obter informações sobre uma VM | Get-AzureRmVM -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Iniciar uma VM | [Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Parar uma VM | [Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Reiniciar uma VM em execução | [Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Excluir uma VM | [Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "resource\_group\_name" -Name "vm\_name"
Generalizar uma VM | [Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "vm\_name" -Generalized<BR></BR><BR></BR>Você deve executar esse comando antes de executar Save-AzureRmVMImage.
Capturar uma VM | [Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "resource\_group\_name" -VMName "vm\_name" -DestinationContainerName "image\_container" -VHDNamePrefix "image\_name\_prefix" -Path "C:\\filepath\\filename.json"<BR></BR><BR></BR>Uma máquina virtual deve ser [desligada e generalizada](virtual-machines-windows-capture-image.md) a fim de ser usada para criar uma imagem. Antes de executar esse comando, execute Set-AzureRmVm.
Atualizar uma VM | [Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "resource\_group\_name" -VM $vm<BR></BR><BR></BR>Obtenha a configuração atual da VM usando Get-AzureRmVM, altere as configurações no objeto da VM e execute este comando.
Adicionar um disco de dados a uma VM | [Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "disk\_name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Usar Get-AzureRmVM para obter o objeto da VM. Especifique o número LUN e o tamanho do disco. Execute Update-AzureRmVM para aplicar as alterações de configuração da VM. O disco que você adiciona não é inicializado, para fazer isso, confira [Gerenciar máquinas virtuais do Azure usando o Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md).
Remover um disco de dados de uma VM | [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "disk\_name"<BR></BR><BR></BR>Use Get-AzureRmVM para obter o objeto da VM. Execute Update-AzureRmVM para aplicar as alterações de configuração da VM.
Adicionar uma extensão a uma VM | [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "resource\_group\_name" -Location "azure\_location" -VMName "vm\_name" -Name "extension\_name" -Publisher "publisher\_name" -Type "extension\_type" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Execute este comando com as [informações de configuração](virtual-machines-windows-extensions-configuration-samples.md) apropriadas para a extensão que você deseja instalar.
Remover uma extensão de VM | [Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "resource\_group\_name" -Name "extension\_name" -VMName "vm\_name"

## Próximas etapas

- Consulte as etapas básicas para criar uma máquina virtual em [Criar uma VM do Windows usando o Resource Manager e o PowerShell](virtual-machines-windows-ps-create.md).

<!---HONumber=AcomDC_0615_2016-->