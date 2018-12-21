---
title: Exemplo da CLI do Azure – Copiar discos gerenciados para uma conta de armazenamento
description: Exemplo da CLI do Azure – Exportar ou copiar discos gerenciados para uma conta de armazenamento.
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 4ee8b9dbb22e4530beb81bea9735867ecf21a1d4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183023"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Exportar/copiar um disco gerenciado para uma conta de armazenamento usando a CLI do Azure

Esse script exporta o VHD subjacente de um disco gerenciado para uma conta de armazenamento na mesma região ou em outra região. Ele primeiro gera o URI do SAS do disco gerenciado e, em seguida, usa-o para copiar o VHD para uma conta de armazenamento. Use esse script para copiar os discos gerenciados para expansão regional. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para gerar o URI de SAS para um disco gerenciado e copia o VHD subjacente para uma conta de armazenamento usando o URI de SAS. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az disk grant-access](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Gera o SAS somente leitura usado para copiar o arquivo VHD subjacente para uma conta de armazenamento ou o baixá-lo localmente  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Copia um blob de forma assíncrona de uma conta de armazenamento para outra |

## <a name="next-steps"></a>Próximas etapas

[Criar um disco gerenciado com base em um VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Criar uma máquina virtual com base em um disco gerenciado](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Linux do Azure](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
