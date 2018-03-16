---
title: "Exemplo de script da CLI do Azure – Obter um grupo de recursos gerenciado e redimensionar VMs | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – Obter um grupo de recursos gerenciado e redimensionar VMs"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 9c4032191738703b71319c05bce4f6bbbc61b44b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Obter recursos em um grupo de recursos gerenciado e redimensionar VMs com a CLI do Azure

Este script recupera os recursos de um grupo de recursos gerenciado e redimensiona as VMs nesse grupo de recursos.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para implantar o aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Get-Help | Observações |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_list) | Liste os aplicativos gerenciados. Forneça os valores da consulta para destacar os resultados. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az_resource_list) | Liste os recursos. Forneça um grupo de recursos e os valores da consulta para destacar os resultados. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az_vm_resize) | Atualize o tamanho de uma máquina virtual. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
