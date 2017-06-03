---
title: "Exemplo de script da CLI do Azure – Criar uma VM, anexando um disco gerenciado como disco do sistema operacional | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – Criar uma VM, anexando um disco gerenciado como disco do sistema operacional"
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
ms.openlocfilehash: 2611698d70a1a5963abaa55c8e9510469c7aa097
ms.contentlocale: pt-br
ms.lasthandoff: 05/31/2017

---

# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>Criar uma máquina virtual usando um disco de sistema operacional gerenciado existente com a CLI

Esse script cria uma máquina virtual anexando um disco gerenciado existente como disco do sistema operacional. Use esse script em cenários anteriores:
* Criar uma VM de um disco de sistema operacional gerenciado existente que foi copiado de um disco gerenciado em uma assinatura diferente
* Criar uma VM de um disco gerenciado existente que foi criado de um arquivo VHD especializado 
* Criar uma VM de um disco do sistema operacional gerenciado existente que foi criado de um instantâneo 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[principal](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Criar VM por meio de um disco gerenciado")]

## <a name="clean-up-deployment"></a>Limpar implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para obter as propriedades do disco gerenciado, anexar um disco gerenciado em uma nova VM e criar uma VM. Cada item em que a tabela contém links para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#show) | Obtém propriedades de disco gerenciado usando o nome do disco e o nome do grupo de recursos. A propriedade ID é usada para anexar um disco gerenciado a uma nova VM |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Cria uma VM usando um disco do sistema operacional gerenciado |
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

