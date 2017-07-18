---
title: Como criar imagens de VM Windows do Azure com o Packer | Microsoft Docs
description: "Saiba como usar o Packer para criar imagens de máquinas virtuais Windows no Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/13/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 035286e74e66cf1eb12a51551fa55a2a0ea5517c
ms.contentlocale: pt-br
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Como usar o Packer para criar imagens de máquina virtual Windows no Azure
Cada VM (máquina virtual) no Azure é criada com base em uma imagem que define a distribuição do Windows e a versão do sistema operacional. As imagens podem incluir configurações e aplicativos pré-instalados. O Azure Marketplace fornece várias imagens internas e de terceiros para os ambientes de sistema operacional e de aplicativo mais comuns ou você pode criar suas próprias imagens personalizadas adequadas às suas necessidades. Este artigo fornece detalhes sobre como usar a ferramenta de software livre [Packer](https://www.packer.io/) para definir e criar imagens personalizadas no Azure.


## <a name="create-supporting-azure-resources"></a>Criar recursos de suporte do Azure
Durante o processo de build, o Packer cria recursos temporários do Azure conforme cria a VM de origem. Para capturar essa VM de origem para uso como uma imagem, você deve definir um grupo de recursos e uma conta de armazenamento. O resultado do processo de build do Packer é armazenado nesse grupo de recursos e nessa conta de armazenamento.

Primeiro, crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

Em seguida, crie uma conta de armazenamento com [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Os nomes da conta de armazenamento devem ser exclusivos, ter entre 3 e 24 caracteres e conter apenas números e letras minúsculas. O exemplo a seguir cria uma conta de armazenamento chamada *mystorageaccount*:

```powershell
$storageAccountName = "mystorageaccount"
New-AzureRmStorageAccount -ResourceGroupName $rgName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"
```


## <a name="create-azure-credentials"></a>Criar as credenciais do Azure
O Packer se autentica no Azure usando uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o Packer. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure.

Crie uma entidade de serviço com [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) e atribua permissões para a entidade de serviço criar e gerenciar recursos com [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment):

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "Azure Packer" -Password "P@ssw0rd!"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Para se autenticar no Azure, você também precisa obter suas IDs de locatário e de assinatura do Azure com [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription):

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId
$sub.SubscriptionId
```

Você pode usar essas duas IDs na próxima etapa.


## <a name="define-packer-template"></a>Definir modelo do Packer
Para criar imagens, você cria um modelo como um arquivo JSON. No modelo, você define construtores e provisionadores que executam o processo de build real. O Packer tem um [provisionador do Azure](https://www.packer.io/docs/builders/azure.html) que permite definir recursos do Azure, como as credenciais da entidade de serviço criadas na etapa anterior.

Crie um arquivo chamado *windows.json* e cole o conteúdo a seguir. Insira seus próprios valores para o seguinte:

| Parâmetro       | Onde obter |
|-----------------|----------------------------------------------------|
| *client_id*      | Exiba a ID da entidade de serviço com `$sp.applicationId` |
| *client_secret*  | Senha especificada em `$securePassword` |
| *tenant_id*      | Saída do comando `$sub.TenantId` |
| *subscription_id* | Saída do comando `$sub.SubscriptionId` |
| *object_id*       | Exiba a ID de objeto da entidade de serviço com `$sp.Id` |
| *storage_account* | Nome especificado em `$storageAccountName` |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": "true",
    "winrm_insecure": "true",
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force}",
      "& $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /shutdown /quiet"
    ]
  }]
}
```

Este modelo cria uma VM Windows Server 2016, instala o IIS e generaliza a VM com o Sysprep.


## <a name="build-packer-image"></a>Criar uma imagem do Packer
Se você ainda não tiver o Packer instalado no computador local, [siga as instruções de instalação do Packer](https://www.packer.io/docs/install/index.html).

Crie a imagem especificando o arquivo de modelo do Packer da seguinte maneira:

```bash
./packer build windows.json
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the certificate's URL ...
==> azure-arm:  -> Key Vault Name        : 'pkrkvbxpchwtl43'
==> azure-arm:  -> Key Vault Secret Name : 'packerKeyVaultSecret'
==> azure-arm:  -> Certificate URL       : 'https://pkrkvbxpchwtl43.vault.azure.net/secrets/packerKeyVaultSecret/6c12261b552c48ebadb7d4a88d99d011'
==> azure-arm: Setting the certificate's URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '40.121.160.214'
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /tmp/packer-powershell-provisioner759984171
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04"><Obj S="progress" RefId="0"><TN RefId="0"><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N="SourceId">1</
I64><PR N="Record"><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd?se=2017-07-13T22%3A25%3A02Z&sig=BYAbOXakavYV%2FbdWYfGqUIdsz2GXivbk0hxG0
5Mc09k%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json?se=2017-07-13T22%3A25%3A02Z&sig=wDMO3aSifbWLSISwoUOfkDMc5z7iKbGV
3us64gGvvlw%3D&sp=r&sr=b&sv=2015-02-21
```

São necessários alguns minutos para que o Packer crie a VM, execute os provisionadores e limpe a implantação.


## <a name="create-azure-image"></a>Criar uma imagem do Azure
O resultado do processo de build do Packer é um VHD (disco rígido virtual) na conta de armazenamento especificada. Crie uma Imagem do Azure com base nesse VHD com [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) e especifique o caminho `OSDiskUri` indicado ao final da saída do build do Packer. O seguinte exemplo cria uma Imagem chamada `myImage`:

```powershell
$osVhdUri = "https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd"
$imageName = "myImage"

$imageConfig = New-AzureRmImageConfig -Location $location
$imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig `
    -OsType "Windows" `
    -OsState "Generalized" `
    -BlobUri $osVhdUri
$image = New-AzureRmImage -ImageName $imageName `
    -ResourceGroupName $rgName `
    -Image $imageConfig
```

Essa Imagem pode ser usada para criar VMs em sua assinatura do Azure. Você não está limitado a criar uma VM no mesmo grupo de recursos da Imagem de origem.


## <a name="create-vm-from-azure-image"></a>Criar uma VM com base em uma Imagem do Azure
Defina o nome de usuário e a senha de um administrador para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential).

```powershell
$cred = Get-Credential
```

Agora você pode criar uma VM com base na Imagem com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo a seguir cria uma VM chamada *myVM* com base em *myImage*.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod "Static" `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```

São necessários alguns minutos para criar a VM.


## <a name="test-vm-and-iis"></a>Testar a VM e o IIS
Obtenha os endereços IP públicos da VM com [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). O exemplo a seguir obtém o endereço IP para *myPublicIP* criado anteriormente:

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIP" | select "IpAddress"
```

Você pode inserir o endereço IP público em um navegador da Web.

![Site do IIS padrão](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você usou o Packer para criar uma imagem de VM com o IIS já instalado. Use essa imagem de VM junto com os fluxos de trabalho de implantação existentes para, por exemplo, implantar o aplicativo em VMs criadas com base na Imagem com o Team Services, Ansible, Chef ou Puppet.

Para obter outros modelos de exemplo do Packer para outras distribuições do Windows, consulte [este repositório GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
