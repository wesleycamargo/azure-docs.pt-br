---
title: "Exemplo de script da CLI do Azure – Criar uma VM de um instantâneo | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – Criar uma VM de um instantâneo"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 960a44e0df2c46cea464d9277f22f449b62e7d06
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017

---

# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Criar uma máquina virtual de um instantâneo com a CLI

Esse script cria uma máquina virtual de um instantâneo de um disco do sistema operacional.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[principal](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Criar VM por meio do instantâneo")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um disco gerenciado, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | Obtém o instantâneo usando o nome do instantâneo e o nome do grupo de recursos. A propriedade de ID do objeto retornado é usada para criar um disco gerenciado.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Cria discos gerenciados de um instantâneo usando ID de instantâneo, nome do disco, tipo de armazenamento e tamanho  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Cria uma VM usando um disco do sistema operacional gerenciado |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

