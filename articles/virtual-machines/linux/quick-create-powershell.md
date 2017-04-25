---
title: "Início Rápido do Azure – Criar PowerShell da VM | Microsoft Docs"
description: "Aprenda rapidamente criar máquinas virtuais Linux com o PowerShell"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: bffbeb5238ec69d763e1cc7ad3c8a6e4fad34306
ms.lasthandoff: 04/12/2017


---

# <a name="create-a-linux-virtual-machine-with-powershell"></a>Aprenda rapidamente a criar máquinas virtuais Linux com o PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando do PowerShell ou em scripts. Este guia detalha o uso do PowerShell para criar uma máquina virtual do Azure executando Ubuntu 14.04 LTS.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) antes de começar.

Além disso, verifique se a versão mais recente do módulo do Azure PowerShell foi instalada. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs):

Por fim, uma chave SSH pública com o nome `id_rsa.pub` precisa ser armazenada no diretório `.ssh` de seu perfil de usuário do Windows. Para obter informações detalhadas sobre a criação de chaves SSH para o Azure, consulte [Criar chaves SSH do Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-networking-resources"></a>Criar recursos de rede

Crie uma rede virtual, sub-rede e um endereço IP público. Esses recursos são usados para fornecer conectividade de rede para a máquina virtual e conectá-la à Internet.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location westeurope `
-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location westeurope `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Crie um grupo de segurança de rede e uma regra de grupo de segurança de rede. O grupo de segurança de rede protege a máquina virtual usando regras de entrada e saída. Nesse caso, uma regra de entrada é criada para a porta 22, que permite conexões SSH. Também queremos criar uma regra de entrada para a porta 80, que permite o tráfego de entrada da Web.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location westeurope `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Criar uma placa de rede para a máquina virtual. A placa de rede conecta a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location westeurope `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma configuração de máquina virtual. Essa configuração inclui as configurações que são usadas ao implantar a máquina virtual como uma imagem, tamanho e configuração de autenticação de máquina virtual.

```powershell
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName myVM -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName Canonical -Offer UbuntuServer -Skus 14.04.2-LTS -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Crie a máquina virtual.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Após a implantação, crie uma conexão SSH com a máquina virtual.

Execute os comandos a seguir para retornar o endereço IP público da máquina virtual.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

De um sistema com o SSH instalado, use o comando a seguir para se conectar à máquina virtual. Se estiver usando o Windows, o [Putty](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-ssh-from-windows?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-private-key-for-putty) poderá ser usado para criar a conexão. 

```bash 
ssh <Public IP Address>
```

Quando solicitado, o nome de usuário de logon é `azureuser`. Se uma frase secreta tiver sido inserida durante a criação de chaves SSH, você precisará digitá-la também.


## <a name="install-nginx"></a>Instalar o NGINX

Use o seguinte script bash para atualizar fontes de pacote e instalar o pacote mais recente do NGINX. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-ngix-welcome-page"></a>Exibir a página de boas-vindas do NGIX

Com o NGINX instalado e a porta 80 que agora está aberta na sua VM da Internet, você pode usar um navegador da Web de sua escolha para exibir a página de boas-vindas do NGINX padrão. Use `publicIpAddress` que você documentou acima para visitar a página padrão. 

![Site padrão NGINX](./media/quick-create-cli/nginx.png) 
## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando não é mais necessário, o comando a seguir pode ser usado para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial Criar máquinas virtuais altamente disponíveis](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explorar amostras do PowerShell de implantação de VM](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

