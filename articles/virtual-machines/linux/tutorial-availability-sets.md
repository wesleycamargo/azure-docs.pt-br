---
title: Tutorial dos conjuntos de disponibilidade para as VMs do Linux no Azure | Microsoft Docs
description: Saiba mais sobre os Conjuntos de disponibilidade para as VMs do Linux no Azure.
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 63fe3f165864f06228604cac56d06cc061ab25f5
ms.contentlocale: pt-br
ms.lasthandoff: 08/28/2017

---

# <a name="how-to-use-availability-sets"></a>Como usar os conjuntos de disponibilidade


Neste tutorial, você aprenderá a aumentar a disponibilidade e a confiabilidade de suas soluções de Máquina Virtual no Azure usando uma capacidade chamada Conjuntos de Disponibilidade. Os Conjuntos de disponibilidade garantem que as VMs implantadas no Azure sejam distribuídas entre vários clusters de hardware isolados. Isso garante que, se ocorrer uma falha de hardware ou de software no Azure, apenas um subconjunto de suas VMs será afetado e a solução geral permanecerá disponível e operacional para seus clientes.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="availability-set-overview"></a>Visão geral do conjunto de disponibilidade

Um Conjunto de disponibilidade é uma funcionalidade de agrupamento lógico que você pode usar no Azure para garantir que os recursos da VM colocados nele sejam isolados uns dos outros quando forem implantados em um datacenter do Azure. O Azure garante que as VMs colocadas em um Conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Isso garante que no caso de falha de hardware ou software do Azure, apenas um subconjunto de suas VMs será afetado e seu aplicativo geral permanecerá disponível e ativo para seus clientes. Os conjuntos de disponibilidade são uma funcionalidade essencial quando você quer compilar soluções de nuvem confiáveis.

Vamos considerar uma solução comum baseada em VM na qual você pode ter quatro servidores Web front-end e usar duas VMs de back-end que hospedam um banco de dados. Com o Azure, convém definir dois conjuntos de disponibilidade antes de implantar suas VMs: um conjunto de disponibilidade para a camada "Web" e um conjunto de disponibilidade para a camada "banco de dados". Ao criar uma nova VM, você pode especificar o conjunto de disponibilidade como um parâmetro para o comando az vm create e o Azure garantirá automaticamente que as VMs criadas dentro do conjunto de disponibilidade sejam isoladas em vários recursos de hardware físico. Isso significa que, se o hardware físico no qual um de seus servidores Web ou VMs do servidor de banco de dados estiverem em execução enfrentar um problema, você saberá que outras instâncias de seu servidor Web e VMs de banco de dados permanecerão em execução, pois estão em um hardware diferente.

Sempre use Conjuntos de disponibilidade quando quiser implantar soluções confiáveis baseadas em VM no Azure.


## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Crie um conjunto de disponibilidade usando [az vm availability-set create](/cli/azure/vm/availability-set#create). Nesse exemplo, definimos o número de domínios de atualização e de falha como *2* para o conjunto de disponibilidade chamado *myAvailabilitySet* no grupo de recursos *myResourceGroupAvailability*.

Crie um grupos de recursos.

```azurecli-interactive 
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Os Conjuntos de disponibilidade permitem que você isole os recursos em "domínios de falha" e "domínios de atualização". Um **domínio de falha** representa uma coleção isolada de recursos de servidor + rede + armazenamento. No exemplo anterior, indicamos que queremos a distribuição de nosso conjunto de disponibilidade em pelo menos dois domínios de falha quando nossas VMs são implantadas. Também podemos indicar que desejamos distribuir nosso conjunto de disponibilidade entre dois **domínios de atualização**.  Dois domínios de atualização garantem que durante a atualização de software do Azure nossos recursos de VM estarão isolados, impedindo que todos os softwares em execução em nossa VM sejam atualizados ao mesmo tempo.

## <a name="configure-virtual-network"></a>Configurar rede virtual
Antes de implantar algumas VMs e poder testar o balanceador, crie os recursos de suporte de rede virtual. Para saber mais sobre redes virtuais, veja o tutorial [Gerenciar Redes Virtuais do Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Criar recursos da rede
Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#create). O exemplo a seguir cria uma rede virtual chamada *myVnet* com uma sub-rede chamada *mySubnet*:

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupAvailability \
    --name myVnet \
    --subnet-name mySubnet
```
NICs virtuais são criadas com [az network nic create](/cli/azure/network/nic#create). O exemplo a seguir cria três NICs virtuais. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir). Você pode criar VMs e NICs virtuais adicionais a qualquer momento e adicioná-las ao balanceador de carga:

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupAvailability \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

## <a name="create-vms-inside-an-availability-set"></a>Criar VMs dentro de um conjunto de disponibilidade

As VMs devem ser criadas dentro do conjunto de disponibilidade para assegurar a distribuição correta pelo hardware. Você não pode adicionar uma VM existente a um conjunto de disponibilidade após sua criação. 

Ao criar uma VM usando [az vm create](/cli/azure/vm#create), você especifica a conjunto de disponibilidade usando o parâmetro `--availability-set` para especificar o nome do conjunto de disponibilidade.

```azurecli-interactive 
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --nics myNic$i \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Agora temos duas máquinas virtuais em nosso conjunto de disponibilidade recém-criado. Como elas estão no mesmo conjunto de disponibilidade, o Azure garantirá que as VMs e todos os seus recursos (incluindo discos de dados) sejam distribuídos entre o hardware físico isolado. Essa distribuição ajuda a garantir uma disponibilidade muito maior de nossa solução de VM geral.

Algo que você pode enfrentar ao adicionar VMs é que um determinado tamanho de VM não pode mais ser usado em seu conjunto de disponibilidade. Esse problema pode ocorrer se não houver capacidade suficiente para adicionar essa VM e preservar ao mesmo tempo as regras de isolamento aplicadas pelo conjunto de disponibilidade. Verifique quais tamanhos de VM estão disponíveis para uso dentro de um conjunto de disponibilidade existente usando o parâmetro `--availability-set list-sizes`.

## <a name="check-for-available-vm-sizes"></a>Conferir os tamanhos de VM disponíveis 

Você pode adicionar posteriormente outras VMs ao conjunto de disponibilidade, mas você precisa saber quais tamanhos de VM estão disponíveis no hardware. Use [az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) para listar todos os tamanhos disponíveis no cluster de hardware para o conjunto de disponibilidade.

```azurecli-interactive 
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis

Avance para o próximo tutorial para saber mais sobre conjuntos de disponibilidade de máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento da VM](tutorial-create-vmss.md)


