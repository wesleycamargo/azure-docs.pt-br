---
title: Dados pessoais do Azure Resource Manager | Microsoft Docs
description: Saiba como gerenciar dados pessoais associados às operações do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: 71928be07080ed14fdcb93f33ea64d2572955b53
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38466017"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Gerenciar dados pessoais associados com o Azure Resource Manager

Para evitar a exposição de informações confidenciais, exclua todas as informações pessoais fornecidas em implantações, grupos de recursos ou marcas. O Azure Resource Manager fornece operações que permitem que você gerencie dados pessoais fornecidos em implantações, grupos de recursos ou marcas.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Excluir dados pessoais no histórico de implantação

Para implantações, o Resource Manager mantém valores de parâmetro e mensagens de status no histórico de implantação. Esses valores são mantidos até que você exclua a implantação do histórico. Para ver se você forneceu dados pessoais nesses valores, lista as implantações. Se você encontrar dados pessoais, exclua as implantações do histórico.

Para listar **implantações** no histórico, use:

* [Listar por Grupo de Recursos](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Get-AzureRmResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

Para excluir **implantações** do histórico, use:

* [Excluir](/rest/api/resources/deployments/delete)
* [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroupDeployment)
* [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Excluir dados pessoais em nomes de grupo de recursos

O nome do grupo de recursos é mantido até que você exclua o grupo de recursos. Para ver se você forneceu dados pessoais nesses valores, lista os grupos de recursos. Se você encontrar dados pessoais, [mova os recursos](resource-group-move-resources.md) para um novo grupo de recursos e exclua o grupo de recursos com os dados pessoais no nome.

Para listar os **grupos de recursos**, use:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/Get-AzureRmResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

Para excluir os **grupos de recursos**, use:

* [Excluir](/rest/api/resources/resourcegroups/delete)
* [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Excluir os dados pessoais em marcas

Nomes e valores de marcas são mantidos até que você exclua ou modifique a marca. Para ver se você forneceu dados pessoais nas marcas, lista as marcas. Se você encontrar dados pessoais, exclua as marcas.

Para listar as **marcas**, use:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzureRmTag](/powershell/module/azurerm.tags/get-azurermtag)
* [az tag list](/cli/azure/tag#az-tag-list)

Para excluir as **marcas**, use:

* [Excluir](/rest/api/resources/tags/delete)
* [Remove-AzureRmTag](/powershell/module/azurerm.tags/remove-azurermtag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral do Azure Resource Manager, confira [O que é o Resource Manager?](resource-group-overview.md)