---
title: Crie um instantâneo de um VHD no Azure | Microsoft Docs
description: Saiba como criar uma cópia de um VHD no Azure como um backup ou para solução de problemas.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/20/2018
ms.author: cynthn
ms.openlocfilehash: e5882b2ddc708544a7715da13c1f0d18384ce4e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-snapshot"></a>Criar um instantâneo 

Capture um instantâneo de um SO ou disco de dados para backup ou para solucionar problemas de VM. Um instantâneo é uma cópia completa somente leitura de um VHD. 

## <a name="use-azure-cli"></a>Usar a CLI do Azure 

O exemplo a seguir exige a CLI 2.0 do Azure instalada e conectada à sua conta do Azure. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

As etapas a seguir mostram como capturar um instantâneo usando o comando `az snapshot create` com o parâmetro `--source-disk`. O exemplo a seguir assume que há uma VM chamada `myVM` no grupo de recursos `myResourceGroup`.

Obtenha a ID do disco.
```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Capture um instantâneo nomeado *osDisk-backup*.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Se você quiser armazenar o instantâneo no armazenamento com resiliência de zona, será necessário criá-lo em uma região que dê suporte para [zonas de disponibilidade](../../availability-zones/az-overview.md) e inclua o parâmetro `--sku Standard_ZRS`.

## <a name="use-azure-portal"></a>Usar o portal do Azure 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Iniciando no canto superior esquerdo, clique em **Criar um recurso** e procure **instantâneo**.
3. Na folha Instantâneo, clique em **Criar**.
4. Insira um **Nome** para o instantâneo.
5. Selecione um [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente ou digite o nome de um novo. 
6. Selecione um Local do datacenter do Azure.  
7. Para **Disco de origem**, selecione o Disco Gerenciado para instantâneo.
8. Escolha o **Tipo de conta** a ser usado para armazenar o instantâneo. É recomendável **Standard_LRS**, a menos que você precise dele armazenado em um disco de alto desempenho.
9. Clique em **Criar**.


## <a name="next-steps"></a>Próximas etapas

 Cria uma máquina virtual de um instantâneo criando um disco gerenciado do instantâneo e, em seguida, anexando o novo disco gerenciado como disco do SO. Para obter mais informações, consulte o script [Criar uma VM com base em um instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

