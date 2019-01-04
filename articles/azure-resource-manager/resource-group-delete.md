---
title: Excluir grupo de recursos e recursos - Azure Resource Manager
description: Descreve como o Azure Resource Manager ordena a exclusão de recursos quando uma exclusão de um grupo de recursos. Descreve os códigos de resposta e como o Resource Manager os manipula para determinar se a exclusão teve êxito.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: b8c4fdc942af291e912a4c1e74d1292279cf9f8c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132322"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Exclusão do grupo de recursos do Azure Resource Manager

Este artigo descreve como o Gerenciador de Recursos do Azure ordena a exclusão de recursos quando você exclui um grupo de recursos.

## <a name="determine-order-of-deletion"></a>Determinar a ordem de exclusão

Quando você exclui um grupo de recursos, o Gerenciador de Recursos determina a ordem para excluir recursos. Ele usa a seguinte ordem:

1. Todos os recursos filho (aninhados) são excluídos.

2. Recursos que gerenciam outros recursos são excluídos a seguir. Um recurso pode ter a propriedade `managedBy` definida para indicar que um recurso diferente a gerencia. Quando essa propriedade é configurada, o recurso que gerencia o outro recurso é excluído antes dos outros recursos.

3. Os recursos restantes são excluídos após as duas categorias anteriores.

## <a name="resource-deletion"></a>Exclusão de recursos

Depois que o pedido é determinado, o Gerenciador de recursos emite uma operação DELETE para cada recurso. Aguarda que quaisquer dependências terminem antes de prosseguir.

Para operações síncronas, os códigos de resposta bem-sucedidos esperados são:

* 200
* 204
* 404

Para operações assíncronas, a resposta bem-sucedida esperada é 202. O Gerenciador de Recursos rastreia o cabeçalho do local ou o cabeçalho da operação azure-async para determinar o status da operação de exclusão assíncrona.
  
### <a name="errors"></a>Errors

Quando uma operação de exclusão retorna um erro, o Gerenciador de Recursos repete a chamada DELETE. As novas tentativas ocorrem para os códigos de status 5xx, 429 e 408. Por padrão, o período de repetição é de 15 minutos.

## <a name="after-deletion"></a>Após a exclusão

Gerenciador de recursos emite uma chamada GET em cada recurso que ele tentou excluir. Espera-se que a resposta desta chamada GET seja 404. Quando o Gerenciador de Recursos recebe um erro 404, ele considera a exclusão concluída com êxito. O Gerenciador de recursos remove o recurso do cache.

No entanto, se a chamada GET no recurso retornar um 200 ou 201, o Gerenciador de recursos recria o recurso.

### <a name="errors"></a>Errors

Se a operação GET retornar um erro, o Gerenciador de Recursos tentará novamente o GET para o seguinte código de erro:

* Menos de 100
* 408
* 429
* Maior que 500

Para outros códigos de erro, o Gerenciador de Recursos falha na exclusão do recurso.

## <a name="next-steps"></a>Próximas etapas

* Para reconhecer os conceitos do Gerenciador de Recursos, consulte [Visão geral do Azure Resource Manager](resource-group-overview.md).
* Para comandos de exclusão, consulte [PowerShell](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup), [CLI do Azure](/cli/azure/group?view=azure-cli-latest#az-group-delete) e [API REST](/rest/api/resources/resourcegroups/delete).
