---
title: "Expandir discos rígidos virtuais em uma VM Linux no Azure | Microsoft Docs"
description: "Saiba como expandir discos rígidos virtuais em uma VM Linux com a CLI do Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 4a0b8254ec80576576afde7af34828025d1d2f0a
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017

---

# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Como expandir discos rígidos virtuais em uma VM Linux com a CLI do Azure
Normalmente, o tamanho do disco rígido virtual padrão do sistema operacional é de 30 GB em uma VM (máquina virtual) do Linux no Azure. É possível [adicionar discos de dados](add-disk.md) para fornecer espaço de armazenamento adicional, mas você também pode desejar expandir o disco do sistema operacional e o disco de dados existente. Este artigo fornece detalhes de como expandir discos gerenciados de uma VM Linux com a CLI do Azure 2.0. Você também pode expandir o disco do sistema operacional não gerenciado com a [CLI do Azure 1.0](expand-disks-nodejs.md).

## <a name="expand-disk"></a>Expandir disco
Certifique-se de que você tenha instalado a versão mais recente da [CLI 2.0 do Azure](/cli/azure/install-az-cli2) e entrado em uma conta do Azure usando [az login](/cli/azure/#login).

Nas amostras a seguir, substitua os nomes de parâmetro de exemplo por seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup* e *myVM*.

1. As operações em discos rígidos virtuais não podem ser executadas com a VM em execução. Desaloque a VM com [az vm deallocate](/cli/azure/vm#deallocate). O exemplo a seguir desaloca a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop` não libera os recursos de computação. Para liberar os recursos de computação, use `az vm deallocate`. A VM deve ser desalocada para que o disco rígido virtual seja expandido.

2. Veja uma lista de discos gerenciados em um grupo de recursos com [az disk list](/cli/azure/disk#list). O exemplo a seguir exibe uma lista de discos gerenciados no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expanda o disco necessário com [az disk update](/cli/azure/disk#update). O exemplo a seguir expande o disco gerenciado chamado *myDataDisk* para ter *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Ao expandir um disco gerenciado, o tamanho atualizado é mapeado para o tamanho de disco gerenciado mais próximo. Para obter uma tabela dos tamanhos de disco gerenciado e as camadas disponíveis, consulte [Visão geral do Azure Managed Disks – Preço e cobrança](../../storage/storage-managed-disks-overview.md#pricing-and-billing).

3. Inicie a VM com [az vm start](/cli/azure/vm#start). O exemplo a seguir inicia a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. SSH da VM com as credenciais apropriadas. Para verificar se o disco do sistema operacional foi redimensionado, use `df -h`. A saída de exemplo a seguir mostra que a unidade de dados (*/dev/sdc1*) agora tem 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## <a name="next-steps"></a>Próximas etapas
Se precisar de armazenamento adicional, também [adicione discos de dados a uma VM do Linux](add-disk.md). Para obter mais informações sobre a criptografia de disco, consulte [Criptografar discos em uma VM do Linux usando a CLI do Azure](encrypt-disks.md).

