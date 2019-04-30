---
title: Criar funções personalizadas para recursos do Azure usando a CLI do Azure | Microsoft Docs
description: Saiba como criar funções personalizadas com o RBAC (controle de acesso baseado em função) para recursos do Azure usando a CLI do Azure. Isso inclui como listar, criar, atualizar e excluir funções personalizadas.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ebced83346a7b130598e4a5f49a72d51ffd18e4f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118766"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Criar funções personalizadas para recursos do Azure usando a CLI do Azure

Se as [funções internas dos recursos do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Este artigo descreve como criar e gerenciar o acesso usando a CLI do Azure.

Para obter um tutorial passo a passo sobre como criar uma função personalizada, confira [Tutorial: Criar uma função personalizada para recursos do Azure usando a CLI do Azure](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar funções personalizadas, você precisará:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de acesso do usuário](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) ou [CLI do Azure](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar as funções personalizadas disponíveis para atribuição, use a [lista de definições de função az](/cli/azure/role/definition#az-role-definition-list). Os exemplos a seguir listam todas as funções personalizadas na assinatura atual.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Listar a definição de uma função personalizada

Para listar uma definição de função personalizada, use [lista de definições de função az](/cli/azure/role/definition#az-role-definition-list). Isso é o mesmo comando que você deseja usar para uma função interna.

```azurecli
az role definition list --name <role_name>
```

A exemplo a seguir lista os *operador de máquina Virtual* definição de função:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

O exemplo a seguir lista apenas as ações do *operador de máquina Virtual* função:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar um arquivo personalizado, use [criar definição de função az](/cli/azure/role/definition#az-role-definition-create). A definição de função pode ser uma descrição de JSON ou um caminho para um arquivo que contém uma descrição de JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

O exemplo a seguir cria uma função personalizada denominada *Operador de Máquina Virtual*. Essa função personalizada atribui acesso a todas as operações de leitura dos provedores de recursos *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network*, além de atribuir acesso para iniciar, reiniciar e monitorar máquinas virtuais. Essa função personalizada pode ser usada em duas assinaturas. Este exemplo utiliza um arquivo JSON como entrada.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, primeiro use [lista de definições de função az](/cli/azure/role/definition#az-role-definition-list) para recuperar a definição de função. Depois, faça as alterações desejadas na definição da função. Por fim, use [atualizar definição de função az](/cli/azure/role/definition#az-role-definition-update) para salvar a definição de função atualizada.

```azurecli
az role definition update --role-definition <role_definition>
```

O exemplo a seguir adiciona a operação *Microsoft.Insights/diagnosticSettings/* a *ações* da função personalizada *Operador de Máquina Virtual*.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

Para excluir um arquivo personalizado, use [excluir definição de função az](/cli/azure/role/definition#az-role-definition-delete). Para especificar a função a ser excluída, use o nome da função ou a ID de função. Para determinar a ID de função, use [lista de definições de função az](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

O exemplo a seguir exclui a função personalizada *Operador de Máquina Virtual*.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Criar uma função personalizada para recursos do Azure usando a CLI do Azure](tutorial-custom-role-cli.md)
- [Funções personalizadas para recursos do Azure](custom-roles.md)
- [Operações do provedor de recursos do Azure Resource Manager](resource-provider-operations.md)