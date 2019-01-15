---
title: Criar uma máquina virtual do Windows usando o PowerShell no Azure Stack | Microsoft Docs
description: Crie uma máquina virtual do Windows com o PowerShell no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 1/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: cddf301db847a5e62afb4383c4da9b10edbfe82f
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306005"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Início rápido: criar uma máquina virtual Windows Server usando o PowerShell no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode criar uma máquina virtual Windows Server 2016 por meio do PowerShell do Azure Stack. Siga as etapas neste artigo para criar e usar uma máquina virtual. Este artigo fornece as etapas para:

* Conecte-se à máquina virtual com um cliente remoto.
* Instalar o servidor web IIS e exibir a página inicial padrão.
* Limpe seus recursos.

>[!NOTE]
 Você pode executar as etapas descritas neste artigo do Kit de desenvolvimento do Azure Stack ou de um cliente externo baseado em Windows, se você estiver conectado por uma VPN.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que o operador do Azure Stack tenha adicionado a **Windows Server 2016** imagem no Marketplace do Azure Stack.

* O Azure Stack requer uma versão específica do Azure PowerShell para criar e gerenciar os recursos. Se você não tiver o PowerShell configurado para o Azure Stack, siga as etapas a serem [instalar](azure-stack-powershell-install.md) PowerShell.

* Com o Azure Stack PowerShell configurado, você precisará se conectar ao ambiente do Azure Stack. Para obter instruções, consulte [conectar-se ao Azure Stack com o PowerShell como um usuário](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual o Azure Stack recursos são implantados e gerenciados. O kit de desenvolvimento ou o sistema integrado do Azure Stack, execute o seguinte bloco de código para criar um grupo de recursos. Os valores são atribuídos para todas as variáveis neste documento, você pode usar esses valores ou atribuir novos valores.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Criar recursos de armazenamento

Crie uma conta de armazenamento e um contêiner de armazenamento para armazenar a imagem do Windows Server 2016.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>Criar recursos de rede

Crie uma rede virtual, sub-rede e um endereço IP público. Esses recursos são usados para fornecer conectividade de rede para a máquina virtual.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Crie um grupo de segurança de rede e uma regra de grupo de segurança de rede

O grupo de segurança de rede protege a máquina virtual por meio de regras de entrada e saídas. Permite criar uma regra de entrada para a porta 3389 para permitir conexões de área de trabalho remota de entrada e uma regra de entrada para a porta 80 para permitir o tráfego da web.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
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
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Crie uma placa de rede para a máquina virtual

A placa de rede conecta a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma configuração de máquina virtual. Essa configuração inclui as configurações usadas ao implantar a máquina virtual. Por exemplo: credenciais, tamanho e a imagem de máquina virtual.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

Remotamente a máquina virtual que você criou na etapa anterior, você precisa de seu endereço IP público. Execute o seguinte comando para obter o endereço IP público da máquina virtual:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Use o seguinte comando para criar uma sessão de área de trabalho remota com a máquina virtual. Substitua o endereço IP pelo publicIPAdress da sua máquina virtual. Quando solicitado, insira o nome de usuário e a senha que você usou ao criar a máquina virtual.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalar o IIS por meio do PowerShell

Agora que você fez logon na VM do Azure, pode usar uma única linha do PowerShell para instalar o IIS e habilitar a regra de firewall local para permitir o tráfego da Web. Abra um promt do PowerShell e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

Com o IIS instalado e com a porta 80 aberta na sua VM, você pode usar um navegador da web de sua escolha para exibir a página de boas-vinda do IIS padrão. Use o *publicIpAddress* documentado na seção anterior para visitar a página padrão.

![Site do IIS padrão](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-virtual-machine"></a>Excluir a máquina virtual

Quando não for mais necessário, use o comando a seguir para remover o grupo de recursos que contém a máquina virtual e seus recursos relacionados:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina de virtual do Windows simples. Para saber mais sobre máquinas virtuais do Azure Stack, continue [considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md).
