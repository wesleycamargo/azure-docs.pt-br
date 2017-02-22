---
title: "Criar uma VM Linux com várias NICs usando a CLI do Azure 2.0 (Visualização) | Microsoft Docs"
description: "Saiba como criar uma VM Linux com várias NICs conectadas a ela usando a CLI do Azure 2.0 (Visualização) ou os modelos do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 368c79b001495e0fb000a4b280023b2299256435
ms.openlocfilehash: a854a15a9119f289344a75638d1042ee6779bb46


---
# <a name="create-a-linux-vm-with-multiple-nics-using-the-azure-cli-20-preview"></a>Criar uma VM Linux com várias NICs usando a CLI do Azure 2.0 (Visualização)
Você pode criar uma VM (máquina virtual) no Azure que tenha várias NICs (interfaces de rede virtual) anexadas a ela. Um cenário comum seria ter sub-redes diferentes para conectividade de front-end e de back-end ou uma rede dedicada a uma solução de monitoramento ou de backup. Este artigo fornece comandos rápidos para criar uma VM com várias NICs anexadas a ela. Para obter informações detalhadas, incluindo como criar várias NICs dentro de seus próprios scripts Bash, leia mais sobre a [implantação de VMs com várias NICs](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamanhos de VM](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dão suporte a um número variável de NICs, sendo assim, dimensione sua VM adequadamente.

> [!WARNING]
> Você deverá anexar várias NICs quando criar a VM – não é possível adicionar NICs a uma VM existente. Você pode [criar uma VM com base nos discos virtuais originais](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e criar várias NICs conforme implantar a VM.


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](virtual-machines-linux-multiple-nics-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- [CLI 2.0 do Azure (Visualização)](#create-supporting-resources) – nossa CLI da próxima geração para o modelo de implantação de gerenciamento de recursos (este artigo)


## <a name="create-supporting-resources"></a>Criar recursos de suporte
Instale a versão mais recente da [CLI do Azure 2.0 (Visualização)](/cli/azure/install-az-cli2) e faça logon na conta do Azure usando [az login](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluíram `myResourceGroup`, `mystorageaccount` e `myVM`.

Primeiramente, crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `WestUS`:

```azurecli
az group create --name myResourceGroup --location westus
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#create). O exemplo a seguir cria uma rede virtual chamada `myVnet` e uma sub-rede chamada `mySubnetFrontEnd`:

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnetFrontEnd --subnet-prefix 192.168.1.0/24
```

Crie uma sub-rede para o tráfego de back-end com [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). O seguinte exemplo cria uma sub-rede chamada `mySubnetBackEnd`:

```azurecli
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVnet \
    --name mySubnetBackEnd --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar várias NICs
Você pode ler mais detalhes sobre a [implantação de várias NICs usando a CLI do Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), incluindo o script do processo de loop para criar todas as NICs.

Normalmente, você cria um [Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md) ou um [balanceador de carga](../load-balancer/load-balancer-overview.md) para ajudar a gerenciar e distribuir o tráfego entre suas VMs. Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg#create). O exemplo a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`:

```azurecli
az network nsg create --resource-group myResourceGroup \
  --name myNetworkSecurityGroup
```

Crie duas NICs com [az network nic create](/cli/azure/network/nic#create). O seguinte exemplo cria duas NICs, chamadas `myNic1` e `myNic2`, conectadas ao grupo de segurança de rede, com uma NIC se conectando a cada sub-rede:

```azurecli
az network nic create --resource-group myResourceGroup --name myNic1 \
  --vnet-name myVnet --subnet mySubnetFrontEnd \
  --network-security-group myNetworkSecurityGroup
az network nic create --resource-group myResourceGroup --name myNic2 \
  --vnet-name myVnet --subnet mySubnetBackEnd \
  --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Criar uma VM e conectar as NICs
Quando você criar a VM, especifica as NICs que criou com `--nics`. Você também precisa tomar cuidado ao selecionar o tamanho da VM. Há limites para o número total de NICs que podem ser adicionados a uma VM. Leia mais sobre [Tamanhos de VM Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Crie uma VM com [az vm create](/cli/azure/vm#create). O seguinte exemplo cria uma VM chamada `myVM` usando o [Managed Disks do Azure](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic1 myNic2
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Criar várias NICs usando modelos do Resource Manager
Os modelos do Azure Resource Manager usam arquivos JSON declarativos para definir o seu ambiente. Você pode ler uma [visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Os modelos do Gerenciador de Recursos oferecem uma maneira de criar várias instâncias de um recurso durante a implantação, como a criação de várias NICs. Você usa *copiar* para especificar o número de instâncias a serem criadas:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre a [criação de várias instâncias usando *copiar*](../azure-resource-manager/resource-group-create-multiple.md). 

Você também pode usar um `copyIndex()` para acrescentar um número a um nome de recurso, o que lhe permite criar `myNic1`, `myNic2` etc. Veja a seguir um exemplo de como acrescentar o valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Você pode ler um exemplo completo em [Criando várias NICs usando modelos do Gerenciador de Recursos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Próximas etapas
Certifique-se de rever os [Tamanhos de VM Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ao tentar criar uma VM com várias NICs. Preste atenção ao número máximo de NICs a que cada VM dá suporte. 

Lembre-se de que você não pode adicionar mais NICs a uma VM existente; você deve criar todas as NICs quando implantar a VM. Tome cuidado ao planejar suas implantações para certificar-se de que tenha toda a conectividade de rede necessária desde o início.




<!--HONumber=Feb17_HO2-->


