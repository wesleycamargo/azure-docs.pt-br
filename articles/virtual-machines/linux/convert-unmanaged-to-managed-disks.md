---
title: "Conversão de VM do Linux no Azure de discos não gerenciados no Managed Disks| Microsoft Docs"
description: "Como converter uma VM do Linux de discos não gerenciados no Azure Managed Disks usando a CLI do Azure 2.0 no modelo de implantação do Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 37c47061b0774d9670b9a2d304d069d5f683c2d2
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Conversão de uma VM do Linux de discos não gerenciados no Azure Managed Disks

Se você tiver máquinas virtuais (VMs) do Linux existentes que usam discos não gerenciados, é possível converter as VMs para usar o [Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Esse processo converte o disco do sistema operacional e os discos de dados anexados.

Este artigo mostra como converter VMs com a CLI do Azure. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta seção aborda como converter suas VMs de instância única do Azure de discos não gerenciados em discos gerenciados. (Consulte a próxima seção se suas VMs estão em uma conjunto de disponibilidade.) Você pode usar este processo para converter discos (SSD) não gerenciados Premium em discos gerenciados Premium ou discos (HDD) não gerenciados Standard em discos gerenciados Standard.

1. Desaloque a VM com [az vm deallocate](/cli/azure/vm#deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converta a VM em discos gerenciados com [az vm convert](/cli/azure/vm#convert). O processo a seguir converte a VM `myVM` incluindo o disco do sistema operacional e os discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Inicie a VM após a conversão em discos gerenciados com [az vm start](/cli/azure/vm#start). O exemplo a seguir inicia a VM `myVM` no grupo de recursos `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estão em um conjunto de disponibilidade, converta primeiro o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

Todas as VMs no conjunto de disponibilidade devem ser desalocadas antes de converter o conjunto de disponibilidade. Planeje a conversão de todas as VMs em discos gerenciados depois da conversão da própria disponibilidade em um conjunto de disponibilidade gerenciada. Então, inicie todas as VMs e continuar a operar normalmente.

1. Liste todas as VMs em um conjunto de disponibilidade com [az vm availability-set list](/cli/azure/vm/availability-set#list). O seguinte exemplo lista todas as VMs em um conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Desaloque todas as VMs com [az vm deallocate](/cli/azure/vm#deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converta um conjunto de disponibilidade com [az vm availability-set convert](/cli/azure/vm/availability-set#convert). O exemplo a seguir converte o conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converta todas as VMs em discos gerenciados com [az vm convert](/cli/azure/vm#convert). O processo a seguir converte a VM `myVM` incluindo o disco do sistema operacional e os discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Inicie todas as VMs após a conversão em discos gerenciados com [az vm start](/cli/azure/vm#start). O exemplo a seguir inicia a VM `myVM` no grupo de recursos `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="managed-disks-and-azure-storage-service-encryption"></a>Managed Disks e criptografia do Serviço de armazenamento do Azure
Você não pode usar as etapas anteriores para converter um disco não gerenciado em um disco gerenciado se o disco não gerenciado estiver em uma conta de armazenamento que foi criptografada usando [Criptografia do serviço de armazenamento do Azure](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). As etapas a seguir especificam como copiar e usar discos não gerenciados que estiveram em uma conta de armazenamento criptografada:

1. Copie o VHD (disco rígido virtual) com [az storage blob copy start](/cli/azure/storage/blob/copy#start) para uma conta de armazenamento que nunca foi habilitada para a Criptografia do Serviço de Armazenamento do Azure.

2. Use a VM copiada de uma das seguintes maneiras:

* Crie uma VM que usa discos gerenciados e especifique este arquivo VHD durante a criação com [az vm create](/cli/azure/vm#create)

* Anexe o VHD copiado com [az vm disk attach](/cli/azure/vm/disk#attach) a uma VM em execução com discos gerenciados.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as opções de armazenamento, confira a [Visão geral dos Azure Managed Disks](../../storage/storage-managed-disks-overview.md).

