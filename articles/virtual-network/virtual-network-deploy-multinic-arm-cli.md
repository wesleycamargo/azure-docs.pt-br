---
title: "Criação de uma VM com várias NICs - CLI 2.0 do Azure | Microsoft Docs"
description: "Aprenda a criar uma VM com várias NICs usando a CLI 2.0 do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19b1757dd694e756cfd2d0d6cd67e64f43ccab7f
ms.lasthandoff: 04/03/2017


---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-20"></a>Como criar uma VM com várias NICs usando a CLI 2.0 do Azure

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md).  Este artigo aborda usando o modelo de implantação do Gerenciador de Recursos, que a Microsoft recomenda para a maioria das novas implantações em vez de do [modelo de implantação clássico](virtual-network-deploy-multinic-classic-cli.md).
>

## <a name="create"></a>Criação da VM

Você pode concluir essa tarefa usando a CLI 2.0 do Azure (este artigo) ou a [CLI 1.0 do Azure](virtual-network-deploy-multinic-cli-nodejs.md). Os valores em "" para as variáveis nas etapas a seguir criam recursos com as configurações do cenário. Altere os valores para adequá-los ao seu ambiente.

1. Instale a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) se você ainda não tiver instalado.
2. Siga as etapas em [Como criar um par de chaves público e privado SSH para VMs do Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar um par de chaves público e privado SSH para VMs do Linux.
3. Faça logon com o comando `az login` de um shell de comando.
4. Execute o script a seguir em um computador Linux ou Mac para criar a VM. O script cria um grupo de recursos, uma rede virtual (VNet) com duas sub-redes, duas NICs e uma VM com duas NICs conectadas a ela. Uma das NICs é conectada a uma sub-rede e é atribuída a um endereço IP estático público e privado. A outra NIC é conectada à outra sub-rede e é atribuída a um endereço IP privado estático e nenhum endereço IP público. A NIC, o endereço IP público, a rede virtual e os recursos da VM devem existir no mesmo local e assinatura. Embora não seja obrigatório que todos os recursos existam no mesmo grupo de recursos, no script a seguir é obrigatório.

```bash
#!/bin/sh

RgName="Multi-NIC-VM"
Location="westus"

# Create a resource group.
az group create \
--name $RgName \
--location $Location

# The address is assigned to the resource from a pool of IP adresses unique to each Azure region. 
# Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists
# the ranges for each region.

PipName="PIP-WEB"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static

# Create a virtual network with one subnet

VnetName="VNet1"
VnetPrefix="10.0.0.0/16"
VnetSubnet1Name="Front-End"
VnetSubnet1Prefix="10.0.0.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnet1Name \
--subnet-prefix $VnetSubnet1Prefix

# Create a second subnet within the VNet

VnetSubnet2Name="Back-end"
VnetSubnet2Prefix="10.0.1.0/24"
az network vnet subnet create \
--vnet-name $VnetName \
--resource-group $RgName \
--name $VnetSubnet2Name \
--address-prefix $VnetSubnet2Prefix

# Create a network interface connected to one of the subnets. The NIC is assigned a single dynamic private and
# public IP address by default, but you can instead, assign static addresses, or no public IP address at all.
# You can also assign multiple private or public IP addresses to each NIC. To learn more about IP addressing
# options for NICs, enter the az network nic create -h command.

Nic1Name="NIC-FE"
PrivateIpAddress1="10.0.0.5"

az network nic create \
--name $Nic1Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress1 \
--public-ip-address $PipName

# Create a second network interface and connect it to the other subnet. Though multiple NICs attached to the same
# VM can be connected to different subnets, the subnets must all be within the same VNet. Add additional NICs as necessary.

Nic2Name="NIC-BE"
PrivateIpAddress2="10.0.1.5"

az network nic create \
--name $Nic2Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet2Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress2

# Create a VM and attach the two NICs.

VmName="WEB"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. Not all VM sizes support
# more than one NIC, so be sure to select a VM size that supports the number of NICs you want to attach to the VM.
# You must create the VM with at least two NICs if you want to add more after VM creation. If you create a VM with
# only one NIC, you can't add additional NICs to the VM after VM creation, regardless of how many NICs the VM supports.
# The VM size specified in the following variable supports two NICs.

VmSize="Standard_DS2"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# az vm image list command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file.

SshKeyValue="~/.ssh/id_rsa.pub"

# Before executing the following command, add variable names of additional NICs you may have added to the script that
# you want to attach to the VM. If creating a Windows VM, remove the **ssh-key-value** line and you'll be prompted for
# the password you want to configure for the VM.

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $Nic1Name $Nic2Name \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Além de criar uma VM com duas NICs, o script cria:
- Um único disco gerenciado premium por padrão, mas há outras opções para você criar outros tipos de disco. Veja o artigo [Como criar uma VM do Linux usando a CLI 2.0 do Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter mais informações.
- Uma rede virtual com duas sub-redes e um único endereço IP público. Como alternativa, você pode usar uma rede virtual, uma sub-rede, uma NIC ou recursos de endereço IP público *existentes*. Para saber como usar os recursos de rede existente em vez de criar recursos adicionais, digite `az vm create -h`.

## <a name = "validate"></a>Como validar a criação da VM e das NICs

1. Digite o comando `az resource list --resouce-group Multi-NIC-VM --output table` para ver uma lista dos recursos criados pelo script. Deve haver seis recursos na saída retornada: duas NICs, um disco, um endereço IP público, uma rede virtual e uma máquina virtual.
2. Digite o comando `az network public-ip show --name PIP-WEB --resource-group Multi-NIC-VM --output table`. Na saída retornada, observe o valor do **IpAddress** e o valor do **PublicIpAllocationMethod** é *estático*.
3. Antes de executar o comando a seguir, remova o <>, substitua *Nome de usuário* pelo nome usado para a variável **Nome de usuário** no script e substitua o *ipAddress* pelo **ipAddress** da etapa anterior. Digite o seguinte comando para conectar com a VM: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 
4. Uma vez conectado com a VM, execute o `sudo ifconfig` comando para ver as interfaces *eth0* e *eth1*. Cada NIC recebeu os endereços IP privados estáticos especificados no script pelos servidores DHCP do Azure. Não altere os endereços IP e MAC atribuídos às NICs até que a VM seja excluída. Recomendamos que você não altere o endereço IP dentro de um sistema operacional, pois pode desabilitar a conectividade com o computador. Os endereços IP públicos não aparecem dentro do sistema operacional, uma vez que eles são traduzidos para e do endereço IP privado pela infraestrutura do Azure.

## <a name= "clean-up"></a>Como remover a VM e os recursos associados

É recomendável excluir os recursos criados neste exercício, caso você não pretenda usá-los em produção. Pode haver cobranças da VM, do endereço IP público e dos recursos de disco enquanto forem provisionados. Para remover os recursos criados durante este exercício, realize as seguintes etapas:
1. Para exibir os recursos do grupo de recursos, execute o comando `az resource list --resource-group Multi-NIC-VM`.
2. Confirme se não existem outros recursos no grupo de recursos, além dos recursos criados pelo script neste artigo. 
3. Para excluir todos os recursos criados neste exercício, execute o comando `az group delete --name Multi-NIC-VM`. O comando exclui o grupo de recursos e todos os recursos que ele contém.

## <a name="next-steps"></a>Próximas etapas

Qualquer tráfego de rede pode fluir de e para a VM criada neste artigo. Você pode definir regras de entrada e saída de um NSG que limitam o tráfego que pode fluir de e para cada adaptador de rede, cada sub-rede ou ambos. Para saber mais sobre NSGs, leia o artigo [Visão geral do NSG](virtual-networks-nsg.md).
