---
title: Exemplo de script da CLI do Azure – Copiar (mover) discos gerenciados para a mesma assinatura ou outra assinatura | Microsoft Docs
description: Exemplo de script da CLI do Azure – Copiar (mover) discos gerenciados para a mesma assinatura ou outra assinatura
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
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: aea21d09aa37eb2f0902df6245e9baebfcbb37fd
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55698558"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Copiar um disco gerenciado para a mesma assinatura ou outra assinatura com a CLI

Esse script copia um disco gerenciado para a mesma assinatura ou outra assinatura, mas na mesma região. A cópia funciona apenas quando as assinaturas fazem parte do mesmo locatário do AAD.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um novo disco gerenciado na assinatura de destino usando a ID do disco gerenciado de origem. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | Obtém todas as propriedades de um disco gerenciado usando o nome e as propriedades do grupo de recursos do disco gerenciado. A propriedade de ID é usada para copiar o disco gerenciado para assinatura diferente.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Copia um disco gerenciado criando um novo disco gerenciado em uma assinatura diferente usando a Id e o nome do disco gerenciado pai.  |

## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual com base em um disco gerenciado](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
