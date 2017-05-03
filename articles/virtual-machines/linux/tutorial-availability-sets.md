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
ms.topic: article
ms.date: 04/20/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 65a7872e0880cc74a00ca1c05baae2b34b407a61
ms.lasthandoff: 04/26/2017


---

# <a name="how-to-use-availability-sets"></a>Como usar os conjuntos de disponibilidade

Neste tutorial, você aprenderá a aumentar a disponibilidade de suas VMs (máquinas virtuais) colocando-as em um agrupamento lógico chamado de conjunto de disponibilidade. Quando você cria VMs em um conjunto de disponibilidade, a plataforma do Azure distribui as VMs na infraestrutura subjacente. Se houver uma falha de hardware ou manutenção planejada na plataforma, o uso dos conjuntos de disponibilidade garante que pelo menos uma VM permaneça em execução.

As etapas neste tutorial podem ser concluídas usando o módulo mais recente do [CLI do Azure 2.0](/cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Visão geral do conjunto de disponibilidade

É possível criar máquinas virtuais em agrupamentos lógicos de hardware no datacenter do Azure subjacente. Quando você cria duas ou mais VMs, seus recursos de computação e armazenamento são distribuídos pelo hardware, como servidores, comutadores de rede e armazenamento. Essa distribuição mantém a disponibilidade de seu aplicativo, caso um componente de hardware passe por manutenção. Os conjuntos de disponibilidade permitem que você defina o agrupamento lógico.

Os conjuntos de disponibilidade oferecem alta disponibilidade às VMs. Você também deve ter certeza de que seus aplicativos foram projetados para tolerar falhas ou eventos de manutenção.

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Crie um conjunto de disponibilidade usando [az vm availability-set create](/cli/azure/availability-set#create). Nesse exemplo, definimos o número de domínios de atualização e de falha como **2** para o conjunto de disponibilidade chamado **myAvailabilitySet** no grupo de recursos **myResourceGroupAvailability**.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

## <a name="create-vms-inside-an-availability-set"></a>Criar VMs dentro de um conjunto de disponibilidade

As VMs precisam ser criadas dentro do conjunto de disponibilidade para assegurar a distribuição correta pelo hardware. Você não pode adicionar uma VM existente a um conjunto de disponibilidade após sua criação. 

O hardware em um local é dividido em vários domínios de atualização e domínios de falha. Um **domínios de atualização** é um grupo de VMs e hardware físico subjacente que podem ser reinicializados simultaneamente. As VMs no mesmo **domínio de falha** compartilham armazenamentos comuns, bem como um comutador de rede e fonte de energia comuns. 

Ao criar uma VM usando [az vm create](/cli/azure/vm#create), você especifica a conjunto de disponibilidade usando o parâmetro `--availability-set` para especificar o nome do conjunto de disponibilidade.

```azurecli
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Agora temos duas máquinas virtuais distribuídas entre o hardware subjacente. 

## <a name="check-for-available-vm-sizes"></a>Conferir os tamanhos de VM disponíveis 

Você pode adicionar posteriormente outras VMs ao conjunto de disponibilidade, mas você precisa saber quais tamanhos de VM estão disponíveis no hardware. Use `az vm availability-set list-sizes` para listar todos os tamanhos disponíveis no cluster de hardware para o conjunto de disponibilidade.

```azurecli
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre como usar conjuntos de disponibilidade. Avance para o próximo tutorial para saber mais sobre conjuntos de disponibilidade de máquinas virtuais.

[Criar um conjunto de dimensionamento da VM](tutorial-create-vmss.md)


