---
title: Exemplo de Script de CLI do Azure - Criar e verificar uma máquina virtual em um laboratório personalizado | Microsoft Docs
description: Esse script de CLI do Azure cria uma máquina virtual em um laboratório personalizado e verifica se ele está disponível.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: bd564cda7b4d5c2158b8499b48b8faa68309b461
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-custom-lab"></a>Use a CLI do Azure para criar e verificar a disponibilidade de uma máquina virtual em um laboratório personalizado

Esse script de CLI do Azure cria uma máquina virtual (VM) em um laboratório personalizado. A VM é criada com base em uma imagem do marketplace com autenticação ssh. O script, em seguida, verifica se a VM está disponível para uso. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az lab vm create ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Cria uma máquina virtual (VM) em um laboratório personalizado. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Exibe o status da VM em um laboratório personalizado. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de script do PowerShell do Azure Lab Services podem ser encontrados em [Exemplos de CLI do Azure Lab Services](../samples-cli.md).