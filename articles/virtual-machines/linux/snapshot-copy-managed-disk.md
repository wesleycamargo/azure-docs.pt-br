---
title: "Crie um instantâneo de um VHD no Azure | Microsoft Docs"
description: "Saiba como criar uma cópia de um VHD no Azure como um backup ou para solução de problemas."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: 152c5a1103d32af27f689086cfcc9cc1a7acc5d3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-snapshot"></a>Criar um instantâneo 

Capture um instantâneo de um VHD de disco de dados ou SO para backup ou solução de problemas de VM. Um instantâneo é uma cópia completa somente leitura de um VHD. 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Usar a CLI 2.0 do Azure para criar um instantâneo

O exemplo a seguir exige a CLI 2.0 do Azure instalada e conectada à sua conta do Azure. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

As etapas a seguir mostram como capturar um instantâneo usando o comando `az snapshot create` com o parâmetro `--source-disk`. O exemplo a seguir pressupõe que exista uma VM chamada `myVM` criada com um disco de SO gerenciado no grupo de recursos `myResourceGroup`.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

A saída deve ser semelhante a esta:

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Usar o Portal do Azure para criar um instantâneo 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Iniciando no canto superior esquerdo, clique em **Criar um recurso** e procure **instantâneo**.
3. Na folha Instantâneo, clique em **Criar**.
4. Insira um **Nome** para o instantâneo.
5. Selecione um [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente ou digite o nome de um novo. 
6. Selecione um Local do datacenter do Azure.  
7. Para **Disco de origem**, selecione o Disco Gerenciado para instantâneo.
8. Escolha o **Tipo de conta** a ser usado para armazenar o instantâneo. É recomendável **Standard_LRS**, a menos que você precise dele armazenado em um disco de alto desempenho.
9. Clique em **Criar**.

Se você planeja usar o instantâneo para criar um Disco Gerenciado e anexá-lo a uma VM que precisa ser de alto desempenho, use o parâmetro `--sku Premium_LRS` com o comando `az snapshot create`. Isso cria o instantâneo para que ele seja armazenado como um Disco Gerenciado Premium. Os Discos Gerenciados Premium têm melhor desempenho porque são SSDs (unidades de estado sólido), mas são mais caros que os HDDs (discos rígidos) Standard.


## <a name="next-steps"></a>Próximas etapas

 Cria uma máquina virtual de um instantâneo criando um disco gerenciado do instantâneo e, em seguida, anexando o novo disco gerenciado como disco do SO. Para obter mais informações, consulte o script [Criar uma VM com base em um instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

