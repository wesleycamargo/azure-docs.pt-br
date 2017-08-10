---
title: "Converter uma máquina virtual Linux no Azure de discos não gerenciados para Managed Disks – Azure Managed Disks | Microsoft Docs"
description: "Como converter uma VM Linux de discos não gerenciados em Managed Disks usando a CLI do Azure 2.0 no modelo de implantação do Resource Manager"
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
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 3109da1dac6ebb6564c94b5c6635ded77ea9be8d
ms.contentlocale: pt-br
ms.lasthandoff: 08/09/2017

---

# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Converter uma máquina virtual Linux de discos não gerenciados em Managed Disks

Se você tiver VMs (máquinas virtuais) Linux existentes que usam discos não gerenciados, é possível converter as VMs para usar os discos gerenciados por meio do serviço [Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Esse processo converte o disco do sistema operacional e os discos de dados anexados.

Este artigo mostra como converter VMs usando a CLI do Azure. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta seção aborda como converter suas VMs de instância única do Azure de discos não gerenciados em discos gerenciados. (Se suas VMs estão em uma conjunto de disponibilidade, consulte a próxima seção.) Você pode usar este processo para converter as VMs de discos (SSD) não gerenciados Premium em Managed Disks Premium ou discos (HDD) não gerenciados Standard em Managed Disks Standard.

1. Desaloque a VM usando [az vm deallocate](/cli/azure/vm#deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Converta a VM em Managed Disks com [az vm convert](/cli/azure/vm#convert). O processo a seguir converte a VM nomeada `myVM`, incluindo o disco do sistema operacional e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Inicie a VM após a conversão em Managed Disks usando [az vm start](/cli/azure/vm#start). O exemplo a seguir inicia a VM `myVM` no grupo de recursos `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estão em um conjunto de disponibilidade, converta primeiro o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

Todas as VMs no conjunto de disponibilidade devem ser desalocadas antes de converter o conjunto de disponibilidade. Planeje a conversão de todas as VMs em Managed Disks após conversão do próprio conjunto de disponibilidade em um conjunto de disponibilidade gerenciada. Então, inicie todas as VMs e continuar a operar normalmente.

1. Liste todas as VMs em um conjunto de disponibilidade usando [az vm availability-set list](/cli/azure/vm/availability-set#list). O seguinte exemplo lista todas as VMs em um conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Desaloque todas as VMs usando [az vm deallocate](/cli/azure/vm#deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converta um conjunto de disponibilidade usando [az vm availability-set convert](/cli/azure/vm/availability-set#convert). O exemplo a seguir converte o conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Converta todas as VMs em Managed Disks usando [az vm convert](/cli/azure/vm#convert). O processo a seguir converte a VM nomeada `myVM`, incluindo o disco do sistema operacional e quaisquer discos de dados:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Inicie todas as VMs após a conversão em Managed Disks usando [az vm start](/cli/azure/vm#start). O seguinte exemplo inicia a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="managed-disks-and-azure-storage-service-encryption"></a>Managed Disks e criptografia do Serviço de armazenamento do Azure
Você não poderá usar as etapas anteriores para converter um disco não gerenciado em um disco gerenciado se o disco não gerenciado estiver em uma conta de armazenamento criptografada usando a [Criptografia do Serviço de Armazenamento do Azure](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). As etapas a seguir especificam como copiar e usar discos não gerenciados que estiveram em uma conta de armazenamento criptografada:

1. Copie o VHD usando [az storage blob copy start](/cli/azure/storage/blob/copy#start) para uma conta de armazenamento que nunca foi habilitada para a Criptografia do Serviço de Armazenamento do Azure.

2. Use a VM copiada de uma das seguintes maneiras:

   * Crie uma VM que usa Managed Disks e especifique este arquivo VHD durante a criação com [az vm create](/cli/azure/vm#create).

   * Anexe o VHD copiado usando [az vm disk attach](/cli/azure/vm/disk#attach) para uma VM em execução que usa Managed Disks.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as opções de armazenamento, confira a [Visão geral dos Azure Managed Disks](../../storage/storage-managed-disks-overview.md).

