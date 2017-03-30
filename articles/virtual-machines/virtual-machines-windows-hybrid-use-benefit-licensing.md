---
title: "Benefício de Uso Híbrido do Azure para o Windows Server e o Windows Client | Microsoft Docs"
description: "Saiba como maximizar os benefícios do Windows Software Assurance para colocar as licenças locais no Azure"
services: virtual-machines-windows
documentationcenter: 
author: george-moore
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 3/10/2017
ms.author: georgem
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: dfa3cf27eebd04507c101fc9421f13dfef39c39f
ms.lasthandoff: 03/21/2017


---
# <a name="azure-hybrid-use-benefit-for-windows-server-and-windows-client"></a>Benefício de Uso Híbrido do Azure para o Windows Server e o Windows Client
Para clientes com o Software Assurance, o Benefício de Uso Híbrido do Azure permite usar as licenças locais do Windows Server e do Windows Client e executar máquinas virtuais Windows no Azure a um custo reduzido. O Benefício de Uso Híbrido do Azure para o Windows Server inclui o Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. O Benefício de Uso Híbrido do Azure para o Windows Client inclui o Windows 10. Para obter mais informações, consulte a página [Licenciamento de Benefício de Uso Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

>[!IMPORTANT]
>Atualmente, o Benefício de Uso Híbrido do Azure para o Windows Client está na Versão prévia. Somente clientes Enterprise com o Windows 10 Enterprise E3/E5 por usuário ou o Windows VDA por usuário (Licenças de Assinatura de Usuário ou Licenças de Assinatura de Usuário Complementares) (“Licenças Aplicáveis”) são qualificados.
>
>

## <a name="ways-to-use-azure-hybrid-use-benefit"></a>Maneiras de usar o Benefício de Uso Híbrido do Azure
Existem algumas maneiras diferentes para implantar VMs Windows com o Benefício de Uso Híbrido do Azure:

1. Se você tiver uma assinatura do Enterprise Agreement, poderá [implantar VMs com base em imagens específicas do Marketplace](#deploy-a-vm-using-the-azure-marketplace) pré-configuradas com o Benefício de Uso Híbrido do Azure.
2. Sem um Contrato Enterprise, é possível [carregar uma VM personalizada](#upload-a-windows-vhd) e [implantá-la usando um modelo do Resource Manager](#deploy-a-vm-via-resource-manager) ou o [Azure PowerShell](#detailed-powershell-deployment-walkthrough).

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>Implantar uma VM usando o Azure Marketplace
Para clientes com [assinaturas do Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx), as imagens estão disponíveis no Marketplace pré-configuradas com o Benefício de Uso Híbrido do Azure. Essas imagens podem ser implantadas diretamente por meio do portal do Azure, de modelos do Resource Manager ou do Azure PowerShell, por exemplo. As imagens no Marketplace são indicadas pelo nome `[HUB]` da seguinte maneira:

![Imagens do Benefício de Uso Híbrido no Azure Marketplace](./media/virtual-machines-windows-hybrid-use-benefit/ahub-images-portal.png)

É possível implantar essas imagens diretamente por meio do portal do Azure. Para uso em modelos do Resource Manager e com o Azure PowerShell, exiba a lista de imagens da seguinte maneira:

Para Windows Server:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "WindowsServer-HUB"
```

Para Windows Client:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "Windows-HUB"
```

Se você não tiver uma assinatura do Enterprise Agreement, continue lendo para obter instruções sobre como carregar uma VM personalizada e implantá-la com o Benefício de Uso Híbrido do Azure.


## <a name="upload-a-windows-vhd"></a>Carregar um VHD do Windows
Para implantar uma VM Windows no Azure, primeiro você precisa criar um VHD que contém o build base do Windows. Esse VHD deve estar preparado adequadamente por meio do Sysprep antes de carregá-lo no Azure. Você pode [ler mais sobre os requisitos de VHD e o processo Sysprep](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Faça backup da VM antes de executar o Sysprep. 

Verifique se você [instalou e configurou o Azure PowerShell mais recente](/powershell/azureps-cmdlets-docs). Depois de preparar o VHD, carregue-o em sua conta de Armazenamento do Azure usando o cmdlet `Add-AzureRmVhd` da seguinte maneira:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> O Microsoft SQL Server, SharePoint Server e Dynamics também podem utilizar o licenciamento Software Assurance. Você ainda precisa preparar a imagem do Windows Server instalando os componentes do aplicativo e fornecendo as chaves de licença corretamente e, então, carregando a imagem do disco no Azure. Examine a documentação apropriada para executar o Sysprep com seu aplicativo, como [Considerações para Instalar o SQL Server usando o Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [Compilar uma Imagem de Referência do SharePoint Server 2016 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).
>
>

Você também pode ler mais sobre como [carregar o VHD no processo do Azure](virtual-machines-windows-upload-image.md#upload-the-vhd-to-your-storage-account)


## <a name="deploy-an-uploaded-vm-via-resource-manager"></a>Implantar uma VM carregada por meio do Resource Manager
Nos modelos do Resource Manager, um parâmetro adicional para `licenseType` pode ser especificado. Você pode ler mais sobre a [criação de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Quando o VHD for carregado no Azure, edite o modelo do Resource Manager para incluir o tipo de licença como parte do provedor de computação e implantar o modelo como normal:

Para Windows Server:
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

Para Windows Client:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-an-uploaded-vm-via-powershell-quickstart"></a>Implantar uma VM carregada por meio do início rápido do PowerShell
Ao implantar a VM do Windows Server por meio do PowerShell, você tem um parâmetro adicional para `-LicenseType`. Depois de carregar o VHD no Azure, você cria uma VM usando `New-AzureRmVM` e especifica o tipo de licenciamento da seguinte maneira:

Para Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Para Windows Client:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

Você pode [ler um passo a passo mais detalhado sobre como implantar uma VM no Azure por meio do PowerShell](virtual-machines-windows-hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough) abaixo ou ler um guia mais descritivo sobre as diferentes etapas para [criar uma VM do Windows usando o Resource Manager e o PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verifique se que sua VM está utilizando o benefício de licenciamento
Depois de implantar sua VM por meio do método de implantação do PowerShell ou do Resource Manager, verifique o tipo de licença com `Get-AzureRmVM` da seguinte maneira:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

A saída é semelhante ao seguinte exemplo do Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Esta saída contrasta com a seguinte VM implantada sem o licenciamento do Benefício de Uso Híbrido do Azure, como uma VM implantada diretamente da Galeria do Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-deployment-walkthrough"></a>Passo a passo detalhado de implantação do PowerShell
As seguintes etapas detalhadas do PowerShell mostram uma implantação completa de uma VM. Você pode ler mais sobre o contexto dos cmdlets reais e sobre a criação de diferentes componentes em [Criar uma VM do Windows usando o Resource Manager e o PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Você passa pela criação de seu grupo de recursos, da conta de armazenamento e da rede virtual e, por fim, define e crie sua VM.

Primeiro, obtenha credenciais com segurança, defina um local e o nome do grupo de recursos:

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

Criar um IP público:

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

Defina sua sub-rede, NIC e VNET:

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Nomeie sua VM e crie uma configuração da VM:

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Defina seu sistema operacional:

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Adicione a NIC à VM:

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Defina a conta de armazenamento a ser usada:

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

Carregue o VHD, preparado adequadamente, e anexe à sua VM para uso:

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Finalmente, crie sua VM e defina o tipo de licenciamento para utilizar o Benefício do uso híbrido do Azure:

Para Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

Para Windows Client:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Client"
```

## <a name="next-steps"></a>Próximas etapas
Leia mais sobre o [Licenciamento do Benefício de Uso Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Saiba mais sobre como [usar os modelos do Resource Manager](../azure-resource-manager/resource-group-overview.md).

