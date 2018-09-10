---
title: Criar funções personalizadas usando a CLI do Azure | Microsoft Docs
description: Saiba como criar funções personalizadas para o controle de acesso baseado em função (RBAC) usando a CLI do Azure. Isso inclui como listar, criar, atualizar e excluir funções personalizadas.
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3b5d18a3e0bf846137dfdf68b8e5dd9e2db58792
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437249"
---
# <a name="create-custom-roles-using-azure-cli"></a>Criar funções personalizadas usando a CLI do Azure

Se as [funções internas](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Este artigo descreve como criar e gerenciar o acesso usando a CLI do Azure.

## <a name="prerequisites"></a>pré-requisitos

Para criar funções personalizadas, você precisará:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de acesso do usuário](built-in-roles.md#user-access-administrator)
- [CLI do Azure](/cli/azure/install-azure-cli) instalada localmente

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

- [Tutorial: Criar uma função personalizada usando a CLI do Azure](tutorial-custom-role-cli.md)
- [Funções personalizadas no Azure](custom-roles.md)
- [Operações do provedor de recursos do Azure Resource Manager](resource-provider-operations.md)