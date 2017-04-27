---
title: "Início Rápido do Azure – Criar PowerShell da VM do Windows | Microsoft Docs"
description: "Aprenda rapidamente criar máquinas virtuais Windows com o PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 91ef7f432d0954cc8456e5d98c48943aa0ad72a7
ms.lasthandoff: 04/12/2017


---

# <a name="create-a-windows-virtual-machine-with-powershell"></a>Aprenda rapidamente a criar máquinas virtuais Windows com o PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando do PowerShell ou em scripts. Este guia detalha o uso do PowerShell para criar uma máquina virtual do Azure executando Windows Server 2016.  Depois que a implantação for concluída, nos conectamos ao servidor e instalamos o IIS.  

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) antes de começar.

Além disso, verifique se a versão mais recente do módulo do Azure PowerShell foi instalada. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs):

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

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Crie uma rede virtual, sub-rede e um endereço IP público. 
Esses recursos são usados para fornecer conectividade de rede para a máquina virtual e conectá-la à Internet.

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

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Crie um grupo de segurança de rede e uma regra de grupo de segurança de rede. 
O grupo de segurança de rede protege a máquina virtual usando regras de entrada e saída. Nesse caso, uma regra de entrada é criada para a porta 3389, que permite conexões de área de trabalho remota.  Também queremos criar uma regra de entrada para a porta 80, que permite o tráfego de entrada da Web.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location westeurope `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Criar uma placa de rede para a máquina virtual. 
A placa de rede conecta a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location westeurope `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma configuração de máquina virtual. Essa configuração inclui as configurações que são usadas ao implantar a máquina virtual como uma imagem, tamanho e configuração de autenticação de máquina virtual. Ao executar esta etapa, credenciais serão solicitadas de você. Os valores que você insere são configurados como o nome de usuário e senha para a máquina virtual.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Crie a máquina virtual.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Após a implantação, crie uma conexão de área de trabalho remota com a máquina virtual.

Execute os comandos a seguir para retornar o endereço IP público da máquina virtual.  Anote esse endereço IP para se conectar a ele com o navegador para testar a conectividade à Web em uma etapa futura.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Use o seguinte comando para criar uma sessão de área de trabalho remota com a máquina virtual. Substitua o endereço IP pelo `publicIPAddress` de sua máquina virtual. Quando solicitado, insira as credenciais usadas ao criar a máquina virtual.

```bash 
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalar o IIS por meio do PowerShell

Agora que você fez logon na VM do Azure, pode usar uma única linha do PowerShell para instalar o IIS e habilitar a regra de firewall local para permitir o tráfego da Web.  Abra um promt do PowerShell e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

Com o IIS instalado e a porta 80 que agora está aberta na sua VM da Internet, você pode usar um navegador da Web de sua escolha para exibir a página de boas-vindas do IIS padrão. Use `publicIpAddress` que você documentou acima para visitar a página padrão. 

![Site do IIS padrão](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando não é mais necessário, o comando a seguir pode ser usado para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial de instalação de uma função e configuração do firewall](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Explorar amostras do PowerShell de implantação de VM](powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
