---
title: "Conversão de uma VM do Linux no Azure de discos não gerenciados em discos gerenciados | Microsoft Docs"
description: "Como converter uma VM de discos não gerenciados em discos gerenciados do Azure usando a CLI 2.0 do Azure"
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
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 6fda4b6e77104b6022b86010b53b46ae5df1b82e
ms.openlocfilehash: 937b22dd9ad26211b006326b39cafe9c5da4e8bd
ms.lasthandoff: 02/27/2017

---

# <a name="how-to-convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Como converter uma VM do Linux de discos não gerenciados em Azure Managed Disks

Se você tiver VMs do Linux existentes no Azure que usam discos não gerenciados nas contas de armazenamento e quiser que essas VMs tenham capacidade de tirar proveito de discos gerenciados, você pode converter as VMs. Esse processo converte o disco do sistema operacional e os discos de dados anexados. O processo de conversão requer uma reinicialização da VM, programe então a migração de suas VMs durante uma janela de manutenção já existente. O processo de migração não é reversível. Faça uma migração de uma máquina virtual de teste antes de executar a migração.

> [!IMPORTANT] 
> Durante a conversão, você pode desalocar a VM. A VM recebe um endereço IP novo quando é iniciada após a conversão. Se você tiver uma dependência em um IP fixo, use um IP reservado.

Você não pode converter um disco não gerenciado em um disco gerenciado se o disco não gerenciado está em uma conta de armazenamento que está, ou esteve, [criptografada usando criptografia de serviço de armazenamento (SSE) do Azure](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). As etapas a seguir especificam como converter discos não gerenciados que estão, ou estiveram, em uma conta de armazenamento criptografada:

- [Copie o disco rígido virtual (VHD)](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unmanaged-disks) com [az storage blob copy start](/cli/azure/storage/blob/copy#start) para uma conta de armazenamento que nunca tenha sido habilitada para criptografia de serviço de armazenamento do Azure.
- Crie uma VM que usa discos gerenciados e especifique este arquivo VHD durante a criação com [az vm create](/cli/azure/vm#create), ou
- Anexe o VHD copiado com [az vm disk attach](/cli/azure/vm/disk#attach) a uma VM em execução com discos gerenciados.

## <a name="convert-vm-to-azure-managed-disks"></a>Conversão de uma VM em Azure Managed Disks
Esta seção aborda como converter suas VMs do Azure existentes de discos não gerenciados em discos gerenciados. Você pode usar este processo para converter discos (SSD) não gerenciados premium em discos gerenciados premium, ou discos (HDD) não gerenciados padrão em discos gerenciados padrão. 

> [!IMPORTANT]
> Depois de executar o procedimento a seguir, há um único blob de blocos que permanece no contêiner de vhds padrão. O nome do arquivo é "VMName.xxxxxxx.status". Não exclua esse objeto de status restante. Um trabalho futuro deverá resolver esse problema.

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

## <a name="convert-vm-in-an-availability-set-to-managed-disks"></a>Conversão da VM em um conjunto de disponibilidade em discos gerenciados

Se as VMs que você deseja converter em discos gerenciados estão em um conjunto de disponibilidade, converta primeiro o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

Todas as VMs no conjunto de disponibilidade devem ser desalocadas antes de converter o conjunto de disponibilidade. Planeje a conversão de todas as VMs em discos gerenciados depois da conversão da própria disponibilidade em um conjunto de disponibilidade gerenciada. Você pode iniciar todas as VMs e continuar a operar normalmente.

1. Liste todas as VMs em um conjunto de disponibilidade com [az vm availability-set list](/cli/azure/vm/availability-set#list). O seguinte exemplo lista todas as VMs em um conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set show --resource-group myResourceGroup \
        --name myAvailabilitySet --query [virtualMachines[*].id] --output table
    ```

2. Desaloque todas as VMs com [az vm deallocate](/cli/azure/vm#deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Converta um conjunto de disponibilidade com [az vm availability-set convert](/cli/azure/vm/availability-set#convert). O exemplo a seguir converte o conjunto de disponibilidade `myAvailabilitySet` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm availability-set convert --resource-group myResourceGroup \
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

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as opções de armazenamento, confira a [Visão geral dos Azure Managed Disks](../storage/storage-managed-disks-overview.md)

