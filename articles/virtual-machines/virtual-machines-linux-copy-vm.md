---
title: Copiar uma VM do Linux usando a CLI do Azure 2.0 | Microsoft Docs
description: "Saiba como criar uma cópia de sua VM Linux do Azure no modelo de implantação do Resource Manager usando a CLI do Azure 2.0 (Visualização)."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1beaca707bfc5058083213b564b3898545f57556
ms.openlocfilehash: edf98951d4ed6709a9894e36ec5ae80b589dd639
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-preview"></a>Criar uma cópia de uma VM do Linux usando a CLI do Azure 2.0 (Visualização)
Este artigo mostra como criar uma cópia de sua VM (máquina virtual) do Azure executando o Linux no modelo de implantação do Azure Resource Manager.

Copie o sistema operacional e os discos de dados para um novo contêiner e configure os recursos de rede e crie a VM.

Você também pode [carregar e criar uma VM com base em uma imagem de disco personalizada](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions"></a>Versões da CLI
Você pode concluir a tarefa usando qualquer uma das seguintes versões de CLI (interface de linha de comando):

* [CLI do Azure 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): para os modelos de implantação clássico e de gerenciamento de recursos.
* CLI 2.0 do Azure (Visualização): nossa CLI da próxima geração para o modelo de implantação de gerenciamento de recursos, abrangido por este artigo.

## <a name="prerequisites"></a>Pré-requisitos
* [CLI do Azure 2.0 (Visualização)](/cli/azure/install-az-cli2), instalada e conectada a uma conta do Azure com [az login](/cli/azure/#login).
* Uma VM do Azure a ser usada como fonte para a cópia.

## <a name="step-1-stop-the-source-vm"></a>Etapa 1: interromper a VM de origem
Desaloque a VM de origem usando [az vm deallocate](/cli/azure/vm#deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos `myResourceGroup`:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Etapa 2: copiar a VM de origem
Para copiar uma máquina virtual, você deve criar uma cópia do disco rígido virtual subjacente. Por meio desse processo, você cria uma VM especializada que contém a mesma configuração e definições que a VM de origem.

O processo de copiar um disco virtual é diferente entre Discos Gerenciados do Azure e discos não gerenciados. Os Managed Disks são tratados pela plataforma do Azure e não exigem preparação ou local para armazenamento. Como os discos gerenciados são um recurso de nível superior, é mais fácil trabalhar com eles. Ou seja, você pode fazer uma cópia direta do recurso de disco.

Para saber mais sobre Azure Managed Disks, veja [Visão geral dos Azure Managed Disks](../storage/storage-managed-disks-overview.md).

Dependendo do tipo de armazenamento da VM de origem, siga as instruções das próximas duas seções e vá para a "Etapa 3: configurar uma rede virtual".

### <a name="managed-disks"></a>Discos gerenciados

1. Lista cada VM e o nome do disco gerenciado pelo sistema operacional com [az vm list](/cli/azure/vm#list). O exemplo a seguir lista todas as VMs no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
```

2. Para copiar o disco, crie um novo disco gerenciado com [az disk create](/cli/azure/disk#create). O exemplo a seguir cria um disco `myCopiedDisk` do disco gerenciado `myDisk`:

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ```

3. Verifique se os discos gerenciados agora em seu grupo de recursos usando [az disk list](/cli/azure/disk#list). O exemplo a seguir lista os discos gerenciados no grupo de recursos `myResourceGroup`:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

4. Vá para a ["Etapa 3: configurar uma rede virtual"](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Discos não gerenciados

1. Para criar uma cópia de um disco rígido virtual, você precisa das chaves da conta de armazenamento do Azure e do URI do disco. Para exibir as chaves da conta de armazenamento, use [az storage account keys list](/cli/azure/storage/account/keys#list).

 O exemplo a seguir lista as chaves para a conta de armazenamento `mystorageaccount` no grupo de recursos `myResourceGroup`:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --name mystorageaccount --output table
    ```

 A saída deverá ser semelhante ao seguinte exemplo:

    ```azurecli
    KeyName    Permissions    Value
    ---------  -------------  ----------------------------------------------------------------------------------------
    key1       Full           gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug==
    key2       Full           UMoyQjWuKCBUAVDr1ANRe/IWTE2o0ZdmQH2JSZzXKNmDKq83368Fw9nyTBcTEkSKI9cm5tlTL8J15ArbPMo8eA==
    ```

2. Para exibir uma lista de VMs e seus URIs, use [az vm list](/cli/azure/vm#list). O exemplo a seguir lista as VMs no grupo de recursos `myResourceGroup`:

    ```azurecli
    az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
    ```

    A saída deverá ser semelhante ao seguinte exemplo:

    ```azurecli
    Name    URI
    ------  -------------------------------------------------------------
    myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
    ```

3. Copie o disco rígido virtual usando [az storage blob copy start](/cli/azure/storage/blob/copy#start). Para fornecer as chaves de conta de armazenamento necessárias e o URI do disco rígido virtual, use as informações das listas `az storage account keys` e `az vm`.

    ```azurecli
    az storage blob copy start \
        --account-name mystorageaccount \
        --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
        --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
        --destination-container vhds --destination-blob myCopiedVHD.vhd
    ```

## <a name="step-3-set-up-a-virtual-network"></a>Etapa 3: configurar uma rede virtual
As etapas opcionais a seguir criam uma nova rede virtual, uma sub-rede, um endereço IP público e uma placa de adaptador de rede virtual (NIC).

Se você estiver copiando uma VM para fins ou implantações adicionais de solução de problemas, não convém usar uma máquina virtual em uma rede virtual existente.

Se quiser criar uma infraestrutura de rede virtual para as VMs copiadas, siga as próximas etapas. Se não quiser criar uma rede virtual, vá para a ["Etapa 4: criar uma VM](#create-a-vm)".

1. Crie a rede virtual usando [az network vnet create](/cli/azure/network/vnet#create). O exemplo a seguir cria uma rede virtual `myVnet` e uma sub-rede `mySubnet`:

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

2. Crie um IP público usando [az network public-ip create](/cli/azure/network/public-ip#create). O exemplo a seguir cria um IP público `myPublicIP` com o nome DNS `mypublicdns`. (O nome DNS deve ser exclusivo; portanto, forneça um nome exclusivo.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

3. Criar a NIC usando [az network nic create](/cli/azure/network/nic#create). O exemplo a seguir cria uma NIC `myNic` anexada à sub-rede `mySubnet`:

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
        --vnet-name myVnet --subnet mySubnet
    ```

## <a name="step-4-create-a-vm"></a>Etapa 4: criar uma VM
Não é possível criar uma VM usando [az vm create](/cli/azure/vm#create). Como ocorre quando você copia um disco, o processo difere ligeiramente entre discos gerenciados e não gerenciados. Dependendo do tipo de armazenamento da VM de origem, siga as instruções das duas próximas seções.

### <a name="managed-disks"></a>Discos gerenciados
1. Criar uma VM usando [az vm create](/cli/azure/vm#create).
2. Especifique o disco copiado gerenciado para usar como o disco do sistema operacional (`--attach-os-disk`) da seguinte maneira:

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image UbuntuLTS
        --attach-os-disk myCopiedDisk
    ```

### <a name="unmanaged-disks"></a>Discos não gerenciados
1. Crie uma VM com [az vm create](/cli/azure/vm#create).
2. Especifique a conta de armazenamento, o nome do contêiner e o disco rígido virtual que você usou quando criou o disco copiado com `az storage blob copy start` (`--image`), da seguinte maneira:

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM  \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
        --use-unmanaged-disk
    ```

## <a name="next-steps"></a>Próximas etapas
Para saber como usar a CLI do Azure para gerenciar sua nova VM, confira [Comandos da CLI do Azure para o Azure Resource Manager](azure-cli-arm-commands.md).

