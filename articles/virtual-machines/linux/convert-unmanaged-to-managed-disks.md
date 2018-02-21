---
title: "Converter uma máquina virtual Linux no Azure de discos não gerenciados para Managed Disks – Azure Managed Disks | Microsoft Docs"
description: "Como converter uma VM Linux de discos não gerenciados em Managed Disks usando a CLI do Azure 2.0 no modelo de implantação do Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 75031b6189710286625406246e6dcde6f1c2b938
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual Linux de discos não gerenciados em Managed Disks

Se você tiver máquinas virtuais (VMs) do Linux existentes que usam discos não gerenciados, é possível converter as VMs para usar o [Azure Managed Disks](../linux/managed-disks-overview.md). Esse processo converte o disco do sistema operacional e os discos de dados anexados.

Este artigo mostra como converter VMs usando a CLI do Azure. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar
* Examine [as perguntas frequentes sobre migração para Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta seção aborda como converter suas VMs de instância única do Azure de discos não gerenciados em discos gerenciados. (Se suas VMs estão em uma conjunto de disponibilidade, consulte a próxima seção.) Você pode usar este processo para converter as VMs de discos (SSD) não gerenciados Premium em Managed Disks Premium ou discos (HDD) não gerenciados Standard em Managed Disks Standard.

1. Desaloque a VM usando [az vm deallocate](/cli/azure/vm#az_vm_deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converta a VM em Managed Disks com [az vm convert](/cli/azure/vm#az_vm_convert). O processo a seguir converte a VM nomeada `myVM`, incluindo o disco do sistema operacional e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Inicie a VM após a conversão em Managed Disks usando [az vm start](/cli/azure/vm#az_vm_start). O exemplo a seguir inicia a VM `myVM` no grupo de recursos `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estão em um conjunto de disponibilidade, converta primeiro o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

Todas as VMs no conjunto de disponibilidade devem ser desalocadas antes de converter o conjunto de disponibilidade. Planeje a conversão de todas as VMs em Managed Disks após conversão do próprio conjunto de disponibilidade em um conjunto de disponibilidade gerenciada. Então, inicie todas as VMs e continuar a operar normalmente.

1. Liste todas as VMs em um conjunto de disponibilidade usando [az vm availability-set list](/cli/azure/vm/availability-set#az_vm_availability_set_list). O seguinte exemplo lista todas as VMs em um conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Desaloque todas as VMs usando [az vm deallocate](/cli/azure/vm#az_vm_deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converta um conjunto de disponibilidade usando [az vm availability-set convert](/cli/azure/vm/availability-set#az_vm_availability_set_convert). O exemplo a seguir converte o conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converta todas as VMs em Managed Disks usando [az vm convert](/cli/azure/vm#az_vm_convert). O processo a seguir converte a VM nomeada `myVM`, incluindo o disco do sistema operacional e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Inicie todas as VMs após a conversão em Managed Disks usando [az vm start](/cli/azure/vm#az_vm_start). O seguinte exemplo inicia a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as opções de armazenamento, confira a [Visão geral dos Azure Managed Disks](../windows/managed-disks-overview.md).
