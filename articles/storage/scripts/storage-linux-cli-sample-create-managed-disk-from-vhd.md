---
title: "Amostra de script da CLI do Azure – Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura | Microsoft Docs"
description: "Amostra de script da CLI do Azure – Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura"
services: managed-disks-linux
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 2e0d668c1fc419abde95207654cab35ea8b058fb
ms.contentlocale: pt-br
ms.lasthandoff: 05/23/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura com a CLI

Este script cria um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura. Use esse script para importar um VHD especializado (não generalizado/do Sysprep) para um disco do sistema operacional gerenciado para criar uma máquina virtual. Ou, use-o para importar um VHD de dados para um disco de dados gerenciados. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/storage/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Criar um disco gerenciado com base em um VHD")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um VHD. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Cria um disco gerenciado usando o URI de um arquivo VHD em uma conta de armazenamento na mesma assinatura |

## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual anexando um disco gerenciado como disco do sistema operacional](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Linux do Azure](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

