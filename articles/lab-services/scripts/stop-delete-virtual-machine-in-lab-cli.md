---
title: Exemplo de Script de CLI do Azure - Interromper e excluir uma máquina virtual em um laboratório personalizado | Microsoft Docs
description: Esse script de CLI do Azure interrompe e exclui uma máquina virtual em um laboratório personalizado.
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
ms.openlocfilehash: 0da99bb5e029f4d1c6be74d09ad11c6017121a7c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-custom-lab"></a>Use a CLI do Azure para interromper e excluir uma máquina virtual em um laboratório personalizado

Esse script de CLI do Azure interrompe e exclui uma máquina virtual (VM) em um laboratório personalizado. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a custom lab")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Interrompe uma máquina virtual (VM) em um laboratório personalizado. Essa operação pode demorar um pouco para ser concluída. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Exclui uma máquina virtual (VM) em um laboratório personalizado. Essa operação pode demorar um pouco para ser concluída. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de script do PowerShell do Azure Lab Services podem ser encontrados em [Exemplos de CLI do Azure Lab Services](../samples-cli.md).