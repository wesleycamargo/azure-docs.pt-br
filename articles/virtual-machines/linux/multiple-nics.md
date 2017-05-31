---
title: "Criar uma VM do Linux no Azure com várias NICs | Microsoft Docs"
description: "Saiba como criar uma VM Linux com várias NICs anexadas a ela usando a CLI 2.0 do Azure ou modelos do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 8a2931e462079c101c91497d459d7d3126234244
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Como criar uma máquina virtual Linux no Azure com várias placas de adaptador de rede
Você pode criar uma VM (máquina virtual) no Azure que tenha várias NICs (interfaces de rede virtual) anexadas a ela. Um cenário comum é ter sub-redes diferentes para conectividade de front-end e de back-end ou uma rede dedicada a uma solução de monitoramento ou de backup. Este artigo fornece detalhes sobre como criar uma VM com várias NICs anexadas a ela e como adicionar ou remover NICs de uma VM existente. Para obter informações detalhadas, incluindo como criar várias NICs dentro de seus próprios scripts Bash, leia mais sobre a [implantação de VMs com várias NICs](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Diferentes [tamanhos de VM](sizes.md) dão suporte a um número variável de NICs, sendo assim, dimensione sua VM adequadamente.

Este artigo fornece detalhes sobre como criar uma VM com várias NICs com a CLI 2.0 do Azure. Você também pode executar essas etapas com a [CLI do Azure 1.0](multiple-nics-nodejs.md).


## <a name="create-supporting-resources"></a>Criar recursos de suporte
Instale a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em uma conta do Azure usando [az login](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *mystorageaccount* e *myVM*.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#create). O exemplo a seguir cria uma rede virtual chamada *myVnet* e uma sub-rede chamada *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Crie uma sub-rede para o tráfego de back-end com [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). O exemplo a seguir cria uma sub-rede chamada *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Crie um grupo de segurança de rede com [az network nsg create](/cli/azure/network/nsg#create). O exemplo a seguir cria um grupo de segurança de rede denominado *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Criar e configurar várias NICs
Crie duas NICs com [az network nic create](/cli/azure/network/nic#create). O exemplo a seguir cria duas NICs, chamadas *myNic1* e *myNic2*, conectadas ao Grupo de Segurança de Rede, com uma NIC se conectando a cada sub-rede:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Criar uma VM e conectar as NICs
Quando você criar a VM, especifica as NICs que criou com `--nics`. Você também precisa tomar cuidado ao selecionar o tamanho da VM. Há limites para o número total de NICs que podem ser adicionados a uma VM. Leia mais sobre [Tamanhos de VM Linux](sizes.md). 

Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

## <a name="add-a-nic-to-a-vm"></a>Adicionar uma NIC a uma VM
As etapas anteriores criaram uma VM com várias NICs. Você também pode adicionar NICs a uma VM existente com a CLI do Azure 2.0. 

Crie outra NIC com [az network nic create](/cli/azure/network/nic#create). O exemplo a seguir cria uma NIC chamada *myNic3* conectada à sub-rede de back-end e ao Grupo de Segurança de Rede criado nas etapas anteriores:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Para adicionar uma NIC a uma VM existente, primeiro desaloque a VM com [az vm deallocate](/cli/azure/vm#deallocate). O exemplo a seguir desaloca a VM chamada *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adicione a NIC com [az vm nic add](/cli/azure/vm/nic#add). O exemplo a seguir adiciona *myNic3* à *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie a VM com [az vm start](/cli/azure/vm#start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

## <a name="remove-a-nic-from-a-vm"></a>Remover uma NIC de uma VM
Para remover uma NIC de uma VM existente, primeiro desaloque a VM com [az vm deallocate](/cli/azure/vm#deallocate). O exemplo a seguir desaloca a VM chamada *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Remova a NIC com [az vm nic remove](/cli/azure/vm/nic#remove). O exemplo a seguir remove *myNic3* de *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM 
    --nics myNic3
```

Inicie a VM com [az vm start](/cli/azure/vm#start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Criar várias NICs usando modelos do Resource Manager
Os modelos do Azure Resource Manager usam arquivos JSON declarativos para definir o seu ambiente. Você pode ler uma [visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Os modelos do Gerenciador de Recursos oferecem uma maneira de criar várias instâncias de um recurso durante a implantação, como a criação de várias NICs. Você usa *copiar* para especificar o número de instâncias a serem criadas:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre a [criação de várias instâncias usando *copiar*](../../resource-group-create-multiple.md). 

Você também pode usar um `copyIndex()` para acrescentar um número a um nome de recurso, o que lhe permite criar `myNic1`, `myNic2` etc. Veja a seguir um exemplo de como acrescentar o valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Você pode ler um exemplo completo em [Criando várias NICs usando modelos do Gerenciador de Recursos](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Próximas etapas
Examine [Tamanhos de VM Linux](sizes.md) ao tentar criar uma VM com várias NICs. Preste atenção ao número máximo de NICs a que cada VM dá suporte. 
