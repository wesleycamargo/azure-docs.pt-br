---
title: "Como redimensionar uma VM Linux com a CLI do Azure 2.0 (Visualização) | Microsoft Docs"
description: "Como escalar ou reduzir verticalmente uma máquina virtual Linux, alterando o tamanho da VM."
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
translationtype: Human Translation
ms.sourcegitcommit: b95ab7b023dddc77231a59151b0c2d44cf968b6e
ms.openlocfilehash: fb2adcfafca35c35d0b526c30d242927b3ef58fe


---
# <a name="how-to-resize-a-linux-vm"></a>Como redimensionar uma VM Linux
Depois de provisionar uma VM (máquina virtual), é possível escalar ou reduzir verticalmente a VM alterando o [tamanho da VM][vm-sizes]. Em alguns casos, você deverá desalocar a VM primeiro. Você precisará desalocar a VM se o tamanho desejado não estiver disponível no cluster de hardware que está hospedando a VM. Este artigo detalha como redimensionar uma VM Linux usando a CLI do Azure 2.0 (Visualização).

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](virtual-machines-linux-change-vm-size-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- [CLI 2.0 do Azure (Visualização)](#resize-a-linux-vm) – nossa CLI da próxima geração para o modelo de implantação de gerenciamento de recursos (este artigo)

## <a name="resize-a-linux-vm"></a>Redimensionar uma VM Linux
Para redimensionar uma VM, é preciso ter a [CLI do Azure 2.0 (Visualização)](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/#login).

1. Veja a lista de tamanhos de VM disponíveis no cluster de hardware onde a VM está hospedada com [az vm list-vm-resize-options](/cli/azure/vm#list-vm-resize-options). O exemplo a seguir lista tamanhos de VM para a VM chamada `myVM` na região `myResourceGroup` do grupo de recursos:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Se o tamanho desejado da VM estiver listado, redimensione a VM com [az vm resize](/cli/azure/vm#resize). O exemplo a seguir redimensiona a VM chamada `myVM` para o tamanho `Standard_DS3_v2`:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    A VM é reiniciada durante esse processo. Após a reinicialização, os discos existentes do sistema operacional e de dados são remapeados. Tudo o que está no disco temporário é perdido.

3. Se o tamanho desejado da VM não estiver listado, você precisará primeiro desalocar a VM com [az vm deallocate](/cli/azure/vm#deallocate). Esse processo permite que a VM seja redimensionada para qualquer tamanho disponível ao qual a região dê suporte e então reiniciada. As etapas a seguir desalocam, redimensionam e iniciam a VM denominada `myVM` no grupo de recursos chamado `myResourceGroup`:
   
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
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md



<!--HONumber=Feb17_HO2-->


