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
ms.date: 04/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: f7fd7d63e6bb1da7022cef782e3b84ea20a64c31
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Criar uma imagem personalizada de uma VM do Azure usando a CLI

Neste tutorial, você aprenderá a criar sua própria imagem personalizada de uma máquina virtual do Azure. Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. Ao criar uma imagem personalizada, a VM, além de todos os discos anexados, são incluídos na imagem. 

As etapas neste tutorial podem ser concluídas usando o módulo mais recente do [CLI do Azure 2.0](/cli/azure/install-azure-cli).

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, este [exemplo de script](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) pode criar uma para você. Ao trabalhar com este tutorial, substitua o grupo de recursos e os nomes de VM onde for necessário.

## <a name="create-an-image"></a>Criar uma imagem

Para criar uma imagem de uma máquina virtual, você precisará preparar a VM desprovisionando, desalocando e, em seguida, marcando a VM de origem como generalizada.

### <a name="deprovision-the-vm"></a>Desprovisionar a VM 

Desprovisionar generaliza a VM removendo informações específicas da máquina. Essa generalização permite implantar várias VMs de uma única imagem. Durante o desprovisionamento, o nome do host é redefinido para `localhost.localdomain`. As chaves de host de SSH, as configurações de nameserver, a senha raiz e as concessões de DHCP em cache também são excluídas.

Para desprovisionar a máquina virtual, use o agente de VM do Azure (waagent). O agente de VM do Azure está instalado na VM e gerencia o provisionamento e a interação com o Azure Fabric Controller. Para saber mais, confira o [Guia do usuário do agente Linux para o Azure](agent-user-guide.md).

Conectar-se à sua VM usando o SSH e executar o comando para desprovisionar a VM. Com o argumento `+user`, a última conta de usuário provisionada e os dados associados também são excluídos. Substitua o endereço IP de exemplo pelo endereço IP público de sua VM.

```bash
ssh azureuser@52.174.34.95 sudo waagent -deprovision+user -force
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

Por fim, defina o estado da VM como generalizada com [az vm generalize](/cli//azure/vm#generalize).
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

### <a name="capture-the-image"></a>Capturar a imagem

Agora você pode criar uma imagem da VM usando [az image create](/cli//azure/image#create). O exemplo a seguir cria uma imagem chamada `myImage` de uma VM chamada `myVM`.
   
```azurecli
az image create --resource-group myResourceGroupImages --name myImage --source myVM
```
 
## <a name="create-a-vm-from-an-image"></a>Criar uma VM de uma imagem

Você pode criar uma VM usando uma imagem com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM `myVMfromImage` da imagem denominada `myImage`.

```azurecli
az vm create --resource-group myResourceGroupImages --name myVMfromImage --image myImage --admin-username azureuser --generate-ssh-keys
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre a criação de imagens de VM personalizadas. Avance para o próximo tutorial para saber mais sobre a alta disponibilidade das máquinas virtuais.

[Criar VMs altamente disponíveis](tutorial-availability-sets.md).


