---
title: Como redimensionar uma VM Linux com a CLI 2.0 do Azure | Microsoft Docs
description: Como escalar ou reduzir verticalmente uma máquina virtual Linux, alterando o tamanho da VM.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 900c45713541825ea36fe0b2d8684863b8138507
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936635"
---
# <a name="resize-a-linux-virtual-machine-using-cli-20"></a>Redimensionar uma máquina virtual do Linux usando a CLI 2.0

Depois de provisionar uma VM (máquina virtual), é possível escalar ou reduzir verticalmente a VM alterando o [tamanho da VM][vm-sizes]. Em alguns casos, você deverá desalocar a VM primeiro. Você precisará desalocar a VM se o tamanho desejado não estiver disponível no cluster de hardware que está hospedando a VM. Este artigo detalha como redimensionar uma VM Linux com a CLI do Azure 2.0. 

## <a name="resize-a-vm"></a>Redimensionar uma VM
Para redimensionar uma VM, é preciso ter a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/reference-index#az_login).

1. Veja a lista de tamanhos de VM disponíveis no cluster de hardware onde a VM está hospedada com [az vm list-vm-resize-options](/cli/azure/vm#az_vm_list_vm_resize_options). O exemplo a seguir lista tamanhos de VM para a VM chamada `myVM` na região `myResourceGroup` do grupo de recursos:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Se o tamanho desejado da VM estiver listado, redimensione a VM com [az vm resize](/cli/azure/vm#az_vm_resize). O exemplo a seguir redimensiona a VM chamada `myVM` para o tamanho `Standard_DS3_v2`:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    A VM é reiniciada durante esse processo. Após a reinicialização, os discos existentes do sistema operacional e de dados são remapeados. Tudo o que está no disco temporário é perdido.

3. Se o tamanho desejado da VM não estiver listado, você precisará primeiro desalocar a VM com [az vm deallocate](/cli/azure/vm#az_vm_deallocate). Esse processo permite que a VM seja redimensionada para qualquer tamanho disponível ao qual a região dê suporte e então reiniciada. As etapas a seguir desalocam, redimensionam e iniciam a VM denominada `myVM` no grupo de recursos chamado `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Desalocar a VM também libera os endereços IP dinâmicos atribuídos à VM. Os discos do sistema operacional e de dados não são afetados.

## <a name="next-steps"></a>Próximas etapas
Para obter escalabilidade adicional, execute várias instâncias de VM e expanda. Para obter mais informações, consulte [Dimensionar automaticamente computadores Linux em um conjunto de dimensionamento de máquinas virtuais][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
