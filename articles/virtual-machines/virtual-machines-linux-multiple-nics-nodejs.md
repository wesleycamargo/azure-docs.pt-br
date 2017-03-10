---
title: "Criar uma VM do Linux no Azure com várias NICs | Microsoft Docs"
description: "Saiba como criar uma VM Linux com várias NICs anexadas usando a CLI do Azure ou modelos do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: e762497c67e04aa4186094a203796278355de36f
ms.openlocfilehash: 5da58eebde8ea0d615514755af1c378d16f60bfc
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-linux-vm-with-multiple-nics-using-the-azure-cli-10"></a>Criar uma VM do Linux com várias NICs usando a CLI do Azure 1.0
Você pode criar uma VM (máquina virtual) no Azure que tenha várias NICs (interfaces de rede virtual) anexadas a ela. Um cenário comum seria ter sub-redes diferentes para conectividade de front-end e de back-end ou uma rede dedicada a uma solução de monitoramento ou de backup. Este artigo fornece comandos rápidos para criar uma VM com várias NICs anexadas a ela. Para obter informações detalhadas, incluindo como criar várias NICs dentro de seus próprios scripts Bash, leia mais sobre a [implantação de VMs com várias NICs](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamanhos de VM](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dão suporte a um número variável de NICs, sendo assim, dimensione sua VM adequadamente.

> [!WARNING]
> Você deverá anexar várias NICs quando criar a VM – não é possível adicionar NICs a uma VM existente. Você pode [criar uma VM com base nos discos virtuais originais](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e criar várias NICs conforme implantar a VM.


## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#create-supporting-resources) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](virtual-machines-linux-multiple-nics.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos


## <a name="create-supporting-resources"></a>Criar recursos de suporte
Verifique se você tem a [CLI do Azure](../xplat-cli-install.md) conectada e está usando o modo do Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluíram `myResourceGroup`, `mystorageaccount` e `myVM`.

Em primeiro lugar, crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `WestUS`:

```azurecli
azure group create myResourceGroup -l WestUS
```

Crie uma conta de armazenamento para manter suas VMs. O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Crie uma rede virtual à qual você conectará suas VMs. O exemplo a seguir cria uma rede virtual chamada `myVnet` com um prefixo de endereço de `192.168.0.0/16`:

```azurecli
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Crie duas sub-redes da rede virtual – uma para o tráfego de front-end e uma para o tráfego de back-end. O exemplo a seguir cria duas sub-redes, chamadas `mySubnetFrontEnd` e `mySubnetBackEnd`:

```azurecli
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar várias NICs
Você pode ler mais detalhes sobre a [implantação de várias NICs usando a CLI do Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), incluindo o script do processo de loop para criar todas as NICs.

O exemplo a seguir cria duas NICs chamadas `myNic1` e `myNic2`, com uma NIC se conectando a cada sub-rede:

```azurecli
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

Normalmente, você também criaria um [Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md) ou [balanceador de carga](../load-balancer/load-balancer-overview.md) para ajudar a gerenciar e distribuir o tráfego entre suas VMs. O exemplo a seguir cria um Grupo de Segurança de Rede denominado `myNetworkSecurityGroup`:

```azurecli
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Associe as NICs ao Grupo de Segurança de Rede usando `azure network nic set`. O exemplo a seguir associa `myNic1` e `myNic2` a `myNetworkSecurityGroup`:

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Criar uma VM e conectar as NICs
Ao criar a VM, agora você especifica várias NICs. Em vez de usar `--nic-name` para fornecer uma única NIC, você usa `--nic-names` e fornece uma lista de NICs separada por vírgulas. Você também precisa tomar cuidado ao selecionar o tamanho da VM. Há limites para o número total de NICs que podem ser adicionados a uma VM. Leia mais sobre [Tamanhos de VM Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). O exemplo a seguir mostra como especificar várias NICs e, em seguida, um tamanho de VM que dê suporte ao uso de várias NICs (`Standard_DS2_v2`):

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
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


