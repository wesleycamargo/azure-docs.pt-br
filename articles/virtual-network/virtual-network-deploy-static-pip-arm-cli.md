---
title: "Criação de uma VM com um endereço IP público estático - CLI 2.0 do Azure | Microsoft Docs"
description: "Aprenda a criar uma VM com um endereço IP público estático usando a interface de linha de comando (CLI) 2.0 do Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: e7874e7d86f75846c452d9863d5604982e9ce50b
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli-20"></a>Como criar uma VM com um endereço IP público estático usando a CLI 2.0 do Azure

> [!div class="op_single_selector"]
- [Portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
- [CLI 2.0 do Azure](virtual-network-deploy-static-pip-arm-cli.md)
- [CLI 1.0 do Azure](virtual-network-deploy-static-pip-cli-nodejs.md)
- [Modelo](virtual-network-deploy-static-pip-arm-template.md)
- [PowerShell (Clássico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo aborda usando o modelo de implantação do Gerenciador de Recursos, que a Microsoft recomenda para a maioria das novas implantações em vez de do modelo de implantação clássico.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>Criação da VM

Você pode concluir essa tarefa usando a CLI 2.0 do Azure (este artigo) ou a [CLI 1.0 do Azure](virtual-network-deploy-static-pip-cli-nodejs.md). Os valores em "" para as variáveis nas etapas a seguir criam recursos com as configurações do cenário. Altere os valores para adequá-los ao seu ambiente.

1. Instale a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) se você ainda não tiver instalado.
2. Siga as etapas em [Como criar um par de chaves público e privado SSH para VMs do Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para criar um par de chaves público e privado SSH para VMs do Linux.
3. Faça logon com o comando `az login` de um shell de comando.
4. Execute o script a seguir em um computador Linux ou Mac para criar a VM. O endereço IP público do Azure, a rede virtual, o adaptador de rede e os recursos da VM devem existir no mesmo local. Embora não seja obrigatório que todos os recursos existam no mesmo grupo de recursos, no script a seguir é obrigatório.

    ```azurecli
    #!/bin/sh

    RgName="IaaSStory"
    Location="westus"
    az group create --name $RgName --location $Location

    # Create a public IP address resource with a static IP address
    PipName="PIPWEB1"
    # Note: The value below must be unique within the azure location it's created in.
    DnsName="iaasstoryws1"

    az network public-ip create \
    --name $PipName \
    --resource-group $RgName \
    --location $Location \

    # The following option allocates a static public IP address to the resource. If you do not specify it, the address is
    # allocated dynamically. The address is assigigned to the resource from a pool of IP adresses unique to each Azure regions.
    # Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 to see the ranges for each region.
    --allocation-method Static \

    --dns-name $DnsName \

    # Create a virtual network with one subnet

    VnetName="TestVNet"
    VnetPrefix="192.168.0.0/16"
    SubnetName="FrontEnd"
    SubnetPrefix="192.168.1.0/24"

    az network vnet create \
    --name $VnetName \
    --resource-group $RgName \
    --location $Location \
    --address-prefix $VnetPrefix \
    --subnet-name $SubnetName \
    --subnet-prefix $SubnetPrefix

    # Create a network interface connected to the VNet with a static private IP address and associate the public IP address
    # resource to the NIC.
    NicName="NICWEB1"
    PrivateIpAddress="192.168.1.101"

    az network nic create \
    --name $NicName \
    --resource-group $RgName \
    --location $Location \
    --subnet $SubnetName \
    --vnet-name $VnetName \
    --private-ip-address $PrivateIpAddress \
    --public-ip-address $PipName

    # Create a new VM with the NIC
    VmName="WEB1"
    
    # Replace the value for the VmSize variable with a value from the
    # https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
    VmSize="Standard_DS1"

    # Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
    # `az vm image list` command. 
    OsImage="credativ:Debian:8:latest"
    
    Username='adminuser'
    
    # Replace the following value with the path to your public key file.
    SshKeyValue="~/.ssh/id_rsa.pub"

    az vm create \
    --name $VmName \
    --resource-group $RgName \
    --image $OsImage \
    --location $Location \
    --size $VmSize \
    --nics $NicName \
    --admin-username $Username \

    # If creating a Windows VM, remove the next line and you'll be prompted for the password you want to configure for the VM.
    --ssh-key-value $SshKeyValue
    ```

    Além de criar uma VM, o script cria:
    - Um único disco gerenciado premium por padrão, mas há outras opções para você criar outros tipos de disco. Veja o artigo [Como criar uma VM do Linux usando a CLI 2.0 do Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter mais informações.
    - A Rede virtual, a sub-rede, a NIC e os recursos de endereço IP público. Como alternativa, você pode usar uma rede virtual, uma sub-rede, uma NIC ou recursos de endereço IP público *existentes*. Para saber como usar os recursos de rede existente em vez de criar recursos adicionais, digite `az vm create -h`.

## <a name = "validate"></a>Como validar a criação da VM e o endereço IP público

1. Digite o comando `az resource list --resouce-group IaaSStory --output table` para ver uma lista dos recursos criados pelo script. Deve haver cinco recursos na saída retornada: um adaptador de rede, um disco, um endereço IP público, uma rede virtual e uma máquina virtual.
2. Digite o comando `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`. Na saída retornada, observe o valor do **IpAddress** e o valor do **PublicIpAllocationMethod** é *estático*.
3. Antes de executar o comando a seguir, remova o <>, substitua *Nome de usuário* pelo nome usado para a variável **Nome de usuário** no script e substitua o *ipAddress* pelo **ipAddress** da etapa anterior. Digite o seguinte comando para conectar com a VM: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`. 

## <a name= "clean-up"></a>Como remover a VM e os recursos associados

Se você criou um grupo de recursos somente para fins de concluir as etapas neste artigo, você pode remover todos os recursos excluindo o grupo de recursos com o comando `az group delete -n IaaSStory`.

>[!WARNING]
>Confira se não existem outros recursos no grupo de recursos, além dos recursos criados pelo script neste artigo, antes de excluir o grupo de recursos. Execute o comando `az resource list --resouce-group IaaSStory` para exibir os recursos no grupo de recursos.

Recomendamos que você exclua os recursos se você não usar a VM em produção. Pode haver cobranças da VM, do endereço IP público e dos recursos de disco enquanto forem provisionados. 

## <a name="next-steps"></a>Próximas etapas

Qualquer tráfego de rede pode fluir de e para a VM criada neste artigo. Você pode definir regras de entrada e saída de um NSG que limitam o tráfego que pode fluir de e para o adaptador de rede, a sub-rede ou ambas. Para saber mais sobre NSGs, leia o artigo [Visão geral do NSG](virtual-networks-nsg.md).
