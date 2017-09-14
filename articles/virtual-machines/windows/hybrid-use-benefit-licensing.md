---
title: "Benefício do Uso Híbrido do Azure para Window Server | Microsoft Docs"
description: "Saiba como maximizar os benefícios do Windows Software Assurance para colocar as licenças locais no Azure"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/26/2017
ms.author: xujing
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: a986ddf22f059dc55bb9bff5c6eaf27324b716cd
ms.contentlocale: pt-br
ms.lasthandoff: 08/31/2017

---
# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Benefício do uso híbrido do Azure para Windows Server
Para clientes com o Software Assurance, o Benefício de Uso Híbrido do Azure permite usar as licenças locais do Windows Server e do Windows Client e executar máquinas virtuais Windows no Azure a um custo reduzido. O Benefício de Uso Híbrido do Azure para o Windows Server inclui o Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Para obter mais informações, consulte a página [Licenciamento de Benefício de Uso Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!NOTE]
> Este artigo mostra a você como implementar o benefício de licenciamento de imagens do Windows Server. Você também pode executar estas etapas para [imagens do Windows 10 Desktop](#windows-desktop-multitenant-hosting-deployment).
>

## <a name="ways-to-use-azure-hybrid-use-benefit"></a>Maneiras de usar o Benefício de Uso Híbrido do Azure
Existem algumas maneiras diferentes para implantar VMs Windows com o Benefício de Uso Híbrido do Azure:

1. Você pode implantar VMs de [imagens específicas do Marketplace](
2. Você pode [carregar uma VM personalizada](#upload-a-windows-vhd) e [implantar usando um modelo do Resource Manager](#deploy-a-vm-via-resource-manager) ou [do Azure PowerShell](#detailed-powershell-deployment-walkthrough).

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>Implantar uma VM usando o Azure Marketplace
Imagens a seguir estão disponíveis no Marketplace pré-configurado com o benefício de uso do Azure Hybrid: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008SP1. Essas imagens podem ser implantadas diretamente do portal do Azure, dos modelos do Resource Manager ou do Azure PowerShell.

É possível implantar essas imagens diretamente por meio do portal do Azure. Para uso em modelos do Resource Manager e com o Azure PowerShell, exiba a lista de imagens da seguinte maneira:

Para Windows Server:
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
- 2016-Datacenter versão 2016.127.20170406 ou superior

- 2012-R2-Datacenter versão 4.127.20170406 ou superior

- 2012-Datacenter versão 3.127.20170406 ou superior

- 2008-R2-SP1 versão 2.127.20170406 ou superior

Para Windows Client:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "Windows-HUB"
```

## <a name="upload-a-windows-server-vhd"></a>Carregar um VHD do Windows Server
Para implantar uma VM do Windows Server no Azure, primeiro você precisa criar um VHD que contém a compilação base do Windows. Esse VHD deve estar preparado adequadamente por meio do Sysprep antes de carregá-lo no Azure. Você pode [ler mais sobre os requisitos de VHD e o processo Sysprep](upload-generalized-managed.md) e [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Faça backup da VM antes de executar o Sysprep. 

Verifique se você [instalou e configurou o Azure PowerShell mais recente](/powershell/azure/overview). Depois de preparar o VHD, carregue-o em sua conta de Armazenamento do Azure usando o cmdlet `Add-AzureRmVhd` da seguinte maneira:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> O Microsoft SQL Server, SharePoint Server e Dynamics também podem utilizar o licenciamento Software Assurance. Você ainda precisa preparar a imagem do Windows Server instalando os componentes do aplicativo e fornecendo as chaves de licença corretamente e, então, carregando a imagem do disco no Azure. Examine a documentação apropriada para executar o Sysprep com seu aplicativo, como [Considerações para Instalar o SQL Server usando o Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [Compilar uma Imagem de Referência do SharePoint Server 2016 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).
>
>

Você também pode ler mais sobre como [carregar o VHD no processo do Azure](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)


## <a name="deploy-a-vm-via-resource-manager-template"></a>Implantar uma VM por meio de um modelo do Resource Manager
Nos modelos do Resource Manager, um parâmetro adicional para `licenseType` pode ser especificado. Você pode ler mais sobre a [criação de modelos do Azure Resource Manager](../../resource-group-authoring-templates.md). Quando o VHD for carregado no Azure, edite o modelo do Resource Manager para incluir o tipo de licença como parte do provedor de computação e implantar o modelo como normal:

Para Windows Server:
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

Para o Cliente Windows, para uso apenas com a Imagem do Azure Marketplace:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Implantar uma VM por meio do início rápido do PowerShell
Ao implantar a VM do Windows Server por meio do PowerShell, você tem um parâmetro adicional para `-LicenseType`. Depois de carregar o VHD no Azure, você cria uma VM usando `New-AzureRmVM` e especifica o tipo de licenciamento da seguinte maneira:

Para Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Para o Cliente Windows, para uso apenas com a Imagem do Azure Marketplace:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

Você pode [ler um passo a passo mais detalhado sobre como implantar uma VM no Azure por meio do PowerShell](hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough) abaixo ou ler um guia mais descritivo sobre as diferentes etapas para [criar uma VM do Windows usando o Resource Manager e o PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


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
As seguintes etapas detalhadas do PowerShell mostram uma implantação completa de uma VM. Você pode ler mais sobre o contexto dos cmdlets reais e sobre a criação de diferentes componentes em [Criar uma VM do Windows usando o Resource Manager e o PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Você passa pela criação de seu grupo de recursos, da conta de armazenamento e da rede virtual e, por fim, define e crie sua VM.

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

## <a name="deploy-a-virtual-machine-scale-set-via-resource-manager-template"></a>Implantar um conjunto de dimensionamento de máquinas virtuais por meio do modelo do Gerenciador de Recursos
Nos modelos do Gerenciador de Recursos VMSS, um parâmetro adicional para `licenseType` deve ser especificado. Você pode ler mais sobre a [criação de modelos do Azure Resource Manager](../../resource-group-authoring-templates.md). Edite seu modelo do Gerenciador de Recursos para incluir a propriedade licenseType como parte do virtualMachineProfile do conjunto de dimensionamento e implantar o modelo como normal – consulte o exemplo abaixo usando a imagem do Windows Server 2016:


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```

> [!NOTE]
> O suporte para implantação de um conjunto de dimensionamento da máquina virtual com benefícios AHUB por meio do PowerShell e de outras ferramentas de SDK está disponível em breve.
>

## <a name="next-steps"></a>Próximas etapas
Leia mais sobre o [Licenciamento do Benefício de Uso Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Saiba mais sobre como [usar os modelos do Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Saiba mais sobre como o [benefício de uso do Azure Hybrid e o Azure Site Recovery tornam a migração de aplicativos do Azure ainda mais econômica](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/).

