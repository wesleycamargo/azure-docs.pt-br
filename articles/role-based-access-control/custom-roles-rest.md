---
title: Criar funções personalizadas usando a API REST - Microsoft Azure | Microsoft Docs
description: Saiba como criar funções personalizadas para o controle de acesso baseado em função (RBAC) usando a API REST. Isso inclui como lista, criar, atualizar e excluir funções personalizadas.
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
ms.openlocfilehash: 8a1bbe8217e2d4a9846f56124e248e19cbe70b19
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436055"
---
# <a name="create-custom-roles-using-the-rest-api"></a>Criar funções personalizadas usando a API REST

Se as [funções internas](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Este artigo descreve como criar e gerenciar o acesso usando a API REST.

## <a name="list-roles"></a>Listar funções

Para listar todas as funções ou obter informações sobre uma única função usando seu nome de exibição, use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) API REST. Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleDefinitions/read` no escopo. Várias [funções internas](built-in-roles.md) recebem acesso a essa operação.

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções.

    | Escopo | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de funções.

    | Filter | DESCRIÇÃO |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Liste funções disponíveis para atribuição no escopo especificado e qualquer de seus escopos filho. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Use a forma codificada da URL do nome de exibição exato da função. Por exemplo, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Obter informações sobre uma função

Para obter informações sobre uma função usando seu identificador de definição de função, use [Definições de Função - Obter ](/rest/api/authorization/roledefinitions/get) API REST. Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleDefinitions/read` no escopo. Várias [funções internas](built-in-roles.md) recebem acesso a essa operação.

Para obter informações sobre uma única função usando seu nome de exibição, consulte a seção [Listar funções](custom-roles-rest.md#list-roles).

1. Use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) API REST para obter o identificador GUID para a função. Para funções internas, você também pode obter o identificador de [Funções Internas](built-in-roles.md).

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja listar as funções.

    | Escopo | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID de definição da função.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, use [Definições de Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) API REST. Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleDefinitions/write` em todo `assignableScopes`. Das funções internas, somente [Proprietário](built-in-roles.md#owner) e [Administrador do Acesso do Usuário](built-in-roles.md#user-access-administrator) recebem permissão para acessar essa operação. 

1. Examine a lista de [operações do provedor de recursos](resource-provider-operations.md) que estão disponíveis para criar as permissões para a função personalizada.

1. Use uma ferramenta GUID para gerar um identificador exclusivo que será usado para o identificador de função personalizada. O identificador tem o formato: `00000000-0000-0000-0000-000000000000`

1. Comece com a solicitação e corpo a seguir:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Dentro da URI, substita *{scope}* pelo primeiro `assignableScopes` da função personalizada.

    | Escopo | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID de definição da função personalizada.

1. Dentro do corpo de solicitação, na `assignableScopes` propriedade, substitua *{roleDefinitionId}* pelo identificador GUID.

1. Substitua *{subscriptionId}* pelo identificador da assinatura.

1. Em `actions` propriedade, adicione as operações que permitem que a função seja executada.

1. Em `notActions` propriedade, adicione as operações que são excluídas do permitidos `actions`.

1. Em `roleName` e `description` propriedades, especifique um nome exclusivo de função e uma descrição. Para obter informações sobre as propriedades, confira [Funções personalizadas](custom-roles.md).

    O exemplo a seguir mostra um exemplo de um corpo de solicitação:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, use [Definições de Função - Criar ou Atualizar](/rest/api/authorization/roledefinitions/createorupdate) API REST. Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleDefinitions/write` em todo `assignableScopes`. Das funções internas, somente [Proprietário](built-in-roles.md#owner) e [Administrador do Acesso do Usuário](built-in-roles.md#user-access-administrator) recebem permissão para acessar essa operação. 

1. Use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) ou [Definições de Função - Obter](/rest/api/authorization/roledefinitions/get) API REST para obter informações sobre a função personalizada. Para mais informações, consulte a seção anterior [Funções de lista](custom-roles-rest.md#list-roles).

1. Comece com a solicitação a seguir:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro da URI, substita *{scope}* pelo primeiro `assignableScopes` da função personalizada.

    | Escopo | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID de definição da função personalizada.

1. Com base nas informações sobre a função personalizada, crie um corpo de solicitação com o seguinte formato:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Atualize o corpo de solicitação com as alterações que você deseja fazer a função personalizada.

    O exemplo a seguir mostra um exemplo de um corpo de solicitação com uma nova ação de configurações de diagnóstico adicionada:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

Para excluir uma função personalizada, use [Definições de Função - Excluir](/rest/api/authorization/roledefinitions/delete) API REST. Para chamar essa API, é necessário ter acesso à operação `Microsoft.Authorization/roleDefinitions/delete` em todo `assignableScopes`. Das funções internas, somente [Proprietário](built-in-roles.md#owner) e [Administrador do Acesso do Usuário](built-in-roles.md#user-access-administrator) recebem permissão para acessar essa operação. 

1. Use [Definições de Função - Lista](/rest/api/authorization/roledefinitions/list) ou [Definições de Função - Obter](/rest/api/authorization/roledefinitions/get) API REST para obter o identificador GUI da função personalizada. Para mais informações, consulte a seção anterior [Funções de lista](custom-roles-rest.md#list-roles).

1. Comece com a solicitação a seguir:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro da URI, substitua *{scope}* pelo escopo para o qual você deseja excluir a função personalizada.

    | Escopo | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{roleDefinitionId}* pelo identificador GUID de definição da função personalizada.

## <a name="next-steps"></a>Próximas etapas

- [Funções personalizadas no Azure](custom-roles.md)
- [Gerenciar acesso usando RBAC e API REST](role-assignments-rest.md)
- [Referência de API REST](/rest/api/azure/)