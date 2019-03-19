---
title: Gerenciar o acesso aos recursos do Azure usando RBAC e a API REST - Azure | Microsoft Docs
description: Saiba como gerenciar o acesso de usuários, grupos e aplicativos com o Azure usando o controle de acesso baseado em função (RBAC) e a API REST. Isso inclui como listar o acesso, conceder o acesso e remover o acesso.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a66969504dee5ba526cdccbbb39471ef5af971c0
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439325"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Gerenciar o acesso aos recursos do Azure usando o RBAC e a API REST

O [RBAC (controle de acesso baseado em função)](overview.md) serve para gerenciar o acesso aos recursos do Azure. Este artigo descreve como gerenciar o acesso de usuários, grupos e aplicativos usando RBAC e API REST.

## <a name="list-access"></a>Relacionar acesso

No RBAC, para listar o acesso, você lista as atribuições de função. Para listar as atribuições de função, use uma das APIs REST [Atribuições de Função - Listar](/rest/api/authorization/roleassignments/list). Para refinar seus resultados, especifique um escopo e um filtro opcional. Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleAssignments/read` no escopo especificado. Várias [funções internas para recursos do Azure](built-in-roles.md) recebem acesso a essa operação.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções de atribuição.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de atribuições de função.

    | Filter | DESCRIÇÃO |
    | --- | --- |
    | `$filter=atScope()` | Liste atribuições de função para apenas o escopo especificado, não incluindo as atribuições de função em subescopos. |
    | `$filter=principalId%20eq%20'{objectId}'` | Liste atribuições de função para um usuário, grupo ou entidade de serviço específica. |
    | `$filter=assignedTo('{objectId}')` | Liste atribuições de função para um usuário específico, incluindo aqueles herdados de grupos. |

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, você cria uma atribuição de função. Para criar uma atribuição de função, use a API REST [Atribuições de Função - Criar](/rest/api/authorization/roleassignments/create) e especifique a entidade de segurança, definição de função e escopo. Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleAssignments/write`. Das funções internas, somente [Proprietário](built-in-roles.md#owner) e [Administrador do Acesso do Usuário](built-in-roles.md#user-access-administrator) recebem permissão para acessar essa operação.

1. Use a API REST [Definições de Função - Listar](/rest/api/authorization/roledefinitions/list) ou veja as [Funções internas](built-in-roles.md) para obter o identificador da definição de função que você deseja atribuir.

1. Use uma ferramenta GUID para gerar um identificador exclusivo que será usado para atribuição de função personalizada. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Comece com a solicitação e corpo a seguir:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. Dentro do URI, substitua *{scope}* pelo escopo da atribuição de função.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleAssignmentName}* pelo identificador GUID da atribuição de função.

1. No corpo da solicitação, substitua *{subscriptionId}* pelo identificador da sua assinatura.

1. Substitua *{roleDefinitionId}* pelo identificador de definição da função.

1. Substitua *{principalId}* com o identificador de objeto do usuário, grupo ou entidade de serviço que receberá a função.

## <a name="remove-access"></a>Remover acesso

No RBAC, para remover o acesso, você deve remover uma atribuição de função. Para remover uma atribuição de função, use a API REST [Atribuição de Função – Excluir](/rest/api/authorization/roleassignments/delete). Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleAssignments/delete`. Das funções internas, somente [Proprietário](built-in-roles.md#owner) e [Administrador do Acesso do Usuário](built-in-roles.md#user-access-administrator) recebem permissão para acessar essa operação.

1. Obtenha o identificador da atribuição de função (GUID). Esse identificador retorna quando você cria pela primeira vez a atribuição de função, ou você pode obtê-lo listando as atribuições de função.

1. Comece com a solicitação a seguir:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para remoção da atribuição de função.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleAssignmentName}* pelo identificador GUID da atribuição de função.

## <a name="next-steps"></a>Próximas etapas

- [Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Referência de API REST do Azure](/rest/api/azure/)
- [Criar funções personalizadas para recursos do Azure usando a API REST](custom-roles-rest.md)