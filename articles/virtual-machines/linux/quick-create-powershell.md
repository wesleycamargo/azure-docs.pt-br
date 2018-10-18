---
title: Início Rápido – Criar uma VM Linux com o Azure PowerShell | Microsoft Docs
description: Neste início rápido, você aprenderá a usar o Azure PowerShell para criar uma máquina virtual do Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 45b1597d3b61a9386fc015b5a7272d948fa5772b
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394775"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Início Rápido: Criar uma máquina virtual do Linux no Azure com o PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando do PowerShell ou em scripts. Este início rápido mostra como usar o módulo do Azure PowerShell para implantar uma VM (máquina virtual) Linux no Azure que executa o Ubuntu. Para ver a VM em ação, você habilita o SSH na VM e instala o servidor Web do NGINX.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

Por fim, uma chave SSH pública com o nome *id_rsa.pub* precisa ser armazenada no diretório *.ssh* do seu perfil de usuário do Windows. Para obter informações detalhadas sobre como criar e usar chaves SSH, confira [Criar chaves SSH para o Azure](ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Criar recursos de rede virtual

Crie uma rede virtual, sub-rede e um endereço IP público. Esses recursos são usados para fornecer conectividade de rede para a VM e conectá-la à Internet:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myVNET" -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Crie uma regra de tráfego e um Grupo de Segurança de Rede do Azure. O Grupo de Segurança de Rede protege a VM com regras de entrada e saída. No exemplo a seguir, uma regra de entrada é criada para a porta TCP 22 que permite conexões SSH. Para permitir o tráfego da Web de entrada, uma regra de entrada para a porta TCP 80 também é criada.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleSSH"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleWWW"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myNetworkSecurityGroup" -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Crie a NIC (adaptador de rede virtual) com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). A NIC virtual conecta a VM a uma sub-rede, um Grupo de Segurança de Rede e um endereço IP público.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name "myNic" -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Uma configuração de máquina virtual inclui as configurações usadas quando uma VM é implantada, como opções de imagem de VM, tamanho e autenticação. Defina as credenciais do SSH, as informações do sistema operacional e o tamanho de VM da seguinte maneira:

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName "myVM" -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Agora, combine as definições de configuração anteriores para criar com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Após a conclusão da implantação, habilite o SSH na VM. Para ver a VM em ação, o servidor Web do NGINX é então instalado.

Para ver o endereço IP público da VM, use o cmdlet [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Use um cliente do SSH para se conectar à VM. Use o Azure Cloud Shell em um navegador da Web ou, se usar o Windows, use o [Putty](ssh-from-windows.md) ou o [Subsistema do Windows para Linux](/windows/wsl/install-win10). Forneça o endereço IP público da VM:

```bash
ssh azureuser@IpAddress
```

Quando solicitado, o nome de usuário de logon é *azureuser*. Se uma frase secreta é usada com as chaves SSH, você precisa inseri-la, quando solicitado.

## <a name="install-web-server"></a>Instalar servidor Web

Para ver a VM em ação, instale o servidor Web do NGINX. Para atualizar fontes de pacote e instalar o último pacote do NGINX, execute os seguintes comandos na sessão do SSH:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Quando terminar, `exit` da sessão do SSH

## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Com o NGINX instalado e a porta 80 agora aberta na VM pela Internet, use um navegador da Web de sua escolha para exibir a página inicial padrão do NGINX. Use o endereço IP público da VM obtido em uma etapa anterior. O seguinte exemplo mostra o site padrão do NGINX:

![Site padrão NGINX](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use o cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, criou um Grupo de Segurança de Rede e uma regra e instalou um servidor Web básico. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Linux.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Linux Azure](./tutorial-manage-vm.md)
