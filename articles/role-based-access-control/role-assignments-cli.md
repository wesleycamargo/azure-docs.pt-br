---
title: Gerenciar acesso usando RBAC e CLI do Azure| Microsoft Docs
description: Saiba como gerenciar acesso de usuários, grupos e aplicativos usando RBAC (controle de acesso baseado em função) e CLI do Azure. Isso inclui como listar o acesso, conceder o acesso e remover o acesso.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 16577339f1aa33fbd1a8b90f4beaef1ee4ce806c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316389"
---
# <a name="manage-access-using-rbac-and-azure-cli"></a>Gerenciar o acesso usando RBAC e CLI do Azure

O [RBAC (controle de acesso baseado em função)](overview.md) é a maneira de gerenciar o acesso aos recursos no Azure. Este artigo descreve como gerenciar o acesso de usuários, grupos e aplicativos usando o RBAC e a CLI do Azure.

## <a name="prerequisites"></a>pré-requisitos

Para gerenciar o acesso, é necessário um dos seguintes itens:

* [Bash no Azure Cloud Shell](/azure/cloud-shell/overview)
* [CLI do Azure](/cli/azure)

## <a name="list-roles"></a>Listar funções

Para listar todas as definições de função disponíveis, use [lista de definições de função az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

O exemplo a seguir lista o nome e a descrição de todas as definições de função disponíveis:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

O exemplo a seguir lista todas as definições de função interna:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role"></a>Relacionar ações de uma função

Para listar as ações de uma definição de função, use [lista de definições de função az](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

A exemplo a seguir lista a definição de função *Colaborador*:

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

A exemplo a seguir lista as *ações* e *não ações* da função *Colaborador*:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

A exemplo a seguir lista as ações da função *Colaborador da Máquia Virtual*:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Relacionar acesso

No RBAC, para listar o acesso, você lista as atribuições de função.

### <a name="list-role-assignments-for-a-user"></a>Listar as atribuições de função de um usuário

Para listar as atribuições de função para um usuário específico, use a [lista de atribuições de função az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Por padrão, somente as atribuições de escopo para a assinatura serão exibidas. Para exibir as atribuições de escopo por grupo ou recurso, use `--all`.

O exemplo a seguir lista as atribuições da função atribuídas diretamente ao usuário *patlong@contoso.com*:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Listar as atribuições para um grupo de recursos

Para listar as atribuições de função que existem para um grupo de recursos, use a [lista de atribuições de função az](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

O exemplo a seguir lista as atribuições de função para o grupo de recursos *pharma-sales-projectforecast*:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="grant-access"></a>Conceder acesso

No RBAC, para conceder acesso, você cria uma atribuição de função.

### <a name="create-a-role-assignment-for-a-user"></a>Criar uma atribuição de função para um usuário

Para criar uma atribuição de função a um usuário no escopo do grupo de recursos, use [criar atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

O exemplo a seguir atribui a função *Colaborador da Máquina Virtual* ao usuário *patlong@contoso.com* no escopo do grupo de recursos *pharma-sales-projectforcast*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>Criar uma atribuição de função para um grupo

Para criar uma atribuição de função para um grupo, use [criar atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo a seguir atribui a função *Leitor* ao grupo *Ann Mack Team* com ID 22222222-2222-2222-2222-222222222222 no escopo da assinatura. Para obter a ID do grupo, você pode usar [lista de grupos do ad az](/cli/azure/ad/group#az-ad-group-list) ou [mostrar de grupo do ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

O exemplo a seguir atribui a função *Colaborador da máquina Virtual* para o grupo *Ann Mack Team* com ID 22222222-2222-2222-2222-222222222222 em um escopo de recursos para uma rede virtual denominada *pharma-sales-project-network*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Criar uma atribuição de função para um aplicativo

Para criar uma função para um aplicativo, use [criar atribuição de função az](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo a seguir atribui a função *Colaborador da Máquina Virtual* a um aplicativo com ID de objeto 44444444-4444-4444-4444-444444444444 no escopo do grupo de recursos *pharma-sales-projectforcast*. Para obter a ID de objeto do aplicativo, você pode usar [lista de aplicativos do ad az](/cli/azure/ad/app#az-ad-app-list) ou [mostrar de aplicativo do ad az](/cli/azure/ad/app#az-ad-app-show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Remover acesso

No RBAC, para remover o acesso, você remove uma atribuição de função usando [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

O exemplo a seguir remove a atribuição da função *Colaborador da Máquina Virtual* do usuário *patlong@contoso.com* no grupo de recursos *pharma-sales-projectforcast*:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

O exemplo a seguir exclui a função *Leitor* do grupo *Ann Mack Team* com ID 22222222-2222-2222-2222-222222222222 no escopo da assinatura. Para obter a ID do grupo, você pode usar [lista de grupos do ad az](/cli/azure/ad/group#az-ad-group-list) ou [mostrar de grupo do ad az](/cli/azure/ad/group#az-ad-group-show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Criar uma função personalizada usando a CLI do Azure](tutorial-custom-role-cli.md)
- [Use a CLI do Azure para gerenciar recursos e grupos de recursos do Azure](../azure-resource-manager/xplat-cli-azure-resource-manager.md)