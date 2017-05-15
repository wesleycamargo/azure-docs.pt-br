---
title: Criar imagens personalizadas da VM com a CLI do Azure | Microsoft Docs
description: Tutorial - Criar uma imagem personalizada da VM usando a CLI do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 2ce92b3f0a21f80eb4294161d6d3a5275c992600
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Criar uma imagem personalizada de uma VM do Azure usando a CLI

Neste tutorial, você aprenderá a definir sua própria imagem personalizada de uma máquina virtual do Azure. As imagens personalizadas permitem criar VMs usando uma imagem que você já tenha configurado. As imagens personalizadas podem ser usadas para inicializar o pré-carregamento de binários e aplicativos, configurações de aplicativos, definições do disco de dados da VM e outras configurações do sistema operacional. Ao criar uma imagem personalizada, a VM que você personaliza e todos os discos anexados são incluídos na imagem.

As etapas neste tutorial podem ser concluídas usando o módulo mais recente do [CLI do Azure 2.0](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

As etapas abaixo detalham como pegar uma máquina virtual existente e transformá-la em uma imagem personalizada reutilizável que você pode usar para criar novas instâncias de VM.

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, este [exemplo de script](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) pode criar uma para você. Ao trabalhar com este tutorial, substitua o grupo de recursos e os nomes de VM onde for necessário.

## <a name="prepare-vm"></a>Preparar VM

Para criar uma imagem de uma máquina virtual, você precisará preparar a VM desprovisionando, desalocando e, em seguida, marcando a VM de origem como generalizada.

### <a name="deprovision-the-vm"></a>Desprovisionar a VM 

Desprovisionar generaliza a VM removendo informações específicas da máquina. Essa generalização permite implantar várias VMs de uma única imagem. Durante o desprovisionamento, o nome do host é redefinido como *localhost.localdomain*. As chaves de host de SSH, as configurações de nameserver, a senha raiz e as concessões de DHCP em cache também são excluídas.

Para desprovisionar a máquina virtual, use o agente de VM do Azure (waagent). O agente de VM do Azure está instalado na VM e gerencia o provisionamento e a interação com o Azure Fabric Controller. Para saber mais, confira o [Guia do usuário do agente Linux para o Azure](agent-user-guide.md).

Conectar-se à sua VM usando o SSH e executar o comando para desprovisionar a VM. Com o argumento `+user`, a última conta de usuário provisionada e os dados associados também são excluídos. Substitua o endereço IP de exemplo pelo endereço IP público de sua VM.

SSH para a VM.
```bash
ssh azureuser@52.174.34.95
```
Desprovisione a VM.

```bash
sudo waagent -deprovision+user -force
```
Feche a sessão do SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desalocar e marcar a VM como generalizada

Para criar uma imagem, a VM precisará ser desalocada. Desaloque a VM usando [az vm deallocate](/cli//azure/vm#deallocate). 
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

Por fim, defina o estado da VM como generalizado com [az vm generalize](/cli//azure/vm#generalize), de maneira que a plataforma do Azure saiba que a VM foi generalizada. Você só pode criar uma imagem por meio de uma VM generalizada.
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

## <a name="create-the-image"></a>Criar a imagem

Agora você pode criar uma imagem da VM usando [az image create](/cli//azure/image#create). O exemplo a seguir cria uma imagem chamada *myImage* por meio de uma VM chamada *myVM*.
   
```azurecli
az image create \
    --resource-group myResourceGroupImages \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Criar VMs por meio da imagem

Agora que tem uma imagem, você pode criar uma ou mais VMs novas por meio da imagem usando [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada *myVMfromImage* por meio da imagem chamada *myImage*.

```azurecli
az vm create \
    --resource-group myResourceGroupImages \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre a criação de imagens de VM personalizadas. Avance para o próximo tutorial para saber mais sobre a alta disponibilidade das máquinas virtuais.

[Criar VMs altamente disponíveis](tutorial-availability-sets.md).


