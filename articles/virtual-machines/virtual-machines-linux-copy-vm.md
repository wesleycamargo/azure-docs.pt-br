---
title: "Copiar uma VM do Linux usando a CLI do Azure 2.0 (Visualização) | Microsoft Azure"
description: "Saiba como criar uma cópia de uma máquina virtual Linux do Azure no modelo de implantação do Resource Manager com a CLI do Azure 2.0 (Visualização)"
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
ms.sourcegitcommit: 344949f10929d9a1ba8f63c5b30f242ddc66fdec
ms.openlocfilehash: 967e2bafdc702c9ddba634089899afee382c5b71


---
# <a name="create-a-copy-of-a-linux-virtual-machine-with-the-azure-cli-20-preview"></a>Criar uma cópia de uma máquina virtual Linux com a CLI do Azure 2.0 (Visualização)
Este artigo mostra como criar uma cópia de sua VM (máquina virtual) do Azure executando o Linux no modelo de implantação do Resource Manager. Primeiro, você copia o sistema operacional e os discos de dados para um novo contêiner e, em seguida, configura os recursos de rede para criar a máquina virtual.

Você também pode [carregar e criar uma VM com base em uma imagem de disco personalizada](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI do Azure 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos
- CLI 2.0 do Azure (Visualização) – nossa CLI da próxima geração para o modelo de implantação de gerenciamento de recursos (este artigo)

## <a name="prerequisites"></a>Pré-requisitos
- É preciso ter a [CLI do Azure 2.0 (Visualização)](/cli/azure/install-az-cli2) mais recente instalada e conectada a uma conta do Azure usando [az login](/cli/azure/#login).
- Você precisa de uma VM do Azure para usar como sua fonte quando você criar uma cópia.

## <a name="stop-the-vm"></a>Pare a VM.
Desaloque a VM de origem com [az vm deallocate](/cli/azure/vm#deallocate). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="copy-the-vm"></a>Copie a VM
Para copiar uma máquina virtual, você deve criar uma cópia do disco rígido virtual subjacente. Esse processo permite que você crie uma VM especializada que contém a mesma configuração e definições, como a VM de origem. O processo de copiar o disco virtual é diferente entre Azure Managed Disks e não gerenciados. Os Managed Disks são tratados pela plataforma do Azure e não exigem nenhuma preparação ou local para armazenamento. Como discos gerenciados são um recurso de nível superior, eles são mais fáceis de trabalhar com - você pode fazer uma cópia direta do recurso de disco. Para saber mais sobre Azure Managed Disks, veja [Visão geral dos Azure Managed Disks](../storage/storage-managed-disks-overview.md). Escolha uma das seguintes etapas apropriadas para o tipo de armazenamento de sua VM de origem:

- [Discos gerenciados](#managed-disks)
- [Discos não gerenciados](#unmanaged-disks) 

### <a name="managed-disks"></a>Discos gerenciados
Lista cada VM e o nome do seu sistema operacional gerenciado disco com [lista de vm az](/cli/azure/vm#list). O exemplo a seguir lista todas as VMs no grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
```

A saída deverá ser semelhante ao seguinte exemplo:

```azurecli
Name    DiskName
------  --------
myVM    myDisk
```

Copie o disco, criando um novo disco gerenciado com [criar disco az](/cli/azure/disk#create). O exemplo a seguir cria um disco chamado `myCopiedDisk` do disco gerenciado chamado `myDisk`:

```azurecli
az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
``` 

Verifique se os discos gerenciados em seu grupo de recursos com [lista de discos az](/cli/azure/disk#list). O exemplo a seguir lista os discos gerenciados no grupo de recursos denominado `myResourceGroup`:

```azurecli
az disk list --resource-group myResourceGroup --output table
```

Mover para [configurações de rede criando e revisando virtual](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Discos não gerenciados
Para criar uma cópia de um VHD, você precisa de chaves de conta de armazenamento e o URI do disco. Use [lista de chaves de conta de armazenamento az](/cli/azure/storage/account/keys#list) para exibir as chaves de conta de armazenamento. O exemplo a seguir lista as chaves para a conta de armazenamento denominada `mystorageaccount` no grupo de recursos denominado`myResourceGroup`

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

Use [lista de vm az](/cli/azure/vm#list) para ver uma lista de máquinas virtuais e seus URIs. O exemplo a seguir lista as VMs no grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
```

A saída deverá ser semelhante ao seguinte exemplo:

```azurecli
Name    URI
------  -------------------------------------------------------------
myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
```

Copie o VHD com [inicial de cópia de blob de armazenamento az](/cli/azure/storage/blob/copy#start). Use as informações de **lista de chaves de conta de armazenamento az** e **lista de vm az** para fornecer as chaves da conta de armazenamento necessária e o URI do VHD.

```azurecli
az storage blob copy start \
    --account-name mystorageaccount \
    --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
    --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
    --destination-container vhds --destination-blob myCopiedVHD.vhd
```

## <a name="set-up-the-virtual-network"></a>Configurar a rede virtual
As etapas a seguir criam uma nova rede virtual, uma sub-rede, um endereço IP público e uma placa de interface de rede virtual (NIC). Essas etapas são opcionais. Se você estiver copiando uma VM para fins ou implantações adicionais de solução de problemas, você não poderá usar uma máquina virtual em uma rede virtual existente. Se você quiser criar uma infra-estrutura de rede virtual para suas VMs copiadas, siga estas próximas etapas, caso contrário, pule para [criar uma máquina virtual](#create-a-vm).

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#create). O exemplo a seguir cria uma rede virtual chamada `myVnet` e uma sub-rede chamada `mySubnet`:

```azurecli
az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
    --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Crie um IP público com [az network public-ip create](/cli/azure/network/public-ip#create). O exemplo a seguir cria um IP público chamado `myPublicIP` com o nome DNS de `mypublicdns`. (O nome DNS deve ser exclusivo; portanto, forneça seu próprio nome exclusivo.)

```azurecli
az network public-ip create --resource-group myResourceGroup --location westus \
    --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Crie a NIC (placa de adaptador de rede) com [az network nic create](/cli/azure/network/nic#create). O exemplo a seguir cria uma NIC denominada `myNic` anexado para o `mySubnet` sub-rede:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
    --vnet-name myVnet --subnet mySubnet
```

## <a name="create-a-vm"></a>Criar uma máquina virtual
Não é possível criar uma VM com [az vm create](/cli/azure/vm#create). Como a cópia de disco, o processo é um pouco diferente entre discos gerenciados e não gerenciados. Crie uma máquina virtual usando um dos seguintes comandos apropriados.

### <a name="managed-disks"></a>Discos gerenciados
Crie uma VM com [az vm create](/cli/azure/vm#create). Especifique o disco copiado gerenciado para usar como o disco do sistema operacional (`--attach-os-disk`) da seguinte maneira:

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image UbuntuLTS
    --attach-os-disk myCopiedDisk
```

### <a name="unmanaged-disks"></a>Discos não gerenciados
Crie uma VM com [az vm create](/cli/azure/vm#create). Especifique a conta de armazenamento, o nome do contêiner e o VHD que você usou ao criar o copiados com **inicial de cópia de blob de armazenamento az** (`--image`) da seguinte maneira:

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM  \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
    --use-unmanaged-disk
```

## <a name="next-steps"></a>Próximas etapas
Para saber como usar a CLI do Azure para gerenciar sua nova máquina virtual, consulte [Comandos da CLI do Azure para o Azure Resource Manager](azure-cli-arm-commands.md).




<!--HONumber=Feb17_HO2-->


