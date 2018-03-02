---
title: "Gerenciar RBAC (controle de acesso baseado em função) com a CLI do Azure | Microsoft Docs"
description: "Saiba como gerenciar o RBAC (Controle de Acesso baseado em função) com a interface de linha de comando do Azure listando as funções e ações de função, e atribuindo funções às assinaturas e escopos de aplicativo."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 5c099a7fd8848c2934603ec9b2db8947885226f9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Gerenciar o Controle de Acesso baseado em função com a Interface de Linha de Comando do Azure

> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)


Com o RBAC (Controle de Acesso Baseado em Função), você define o acesso para usuários, grupos e entidades de serviço, atribuindo funções em um escopo específico. Este artigo descreve como gerenciar acesso usando a interface de linha de comando do Azure (CLI).

## <a name="prerequisites"></a>pré-requisitos

Para usar a CLI do Azure para gerenciar o RBAC, é necessário ter os seguintes pré-requisitos :

* [CLI 2.0 do Azure](/cli/azure/overview). Você pode usá-lo no seu navegador com o [Azure Cloud Shell](../cloud-shell/overview.md) ou pode [instalá-lo](/cli/azure/install-azure-cli) no macOS, Linux e Windows e executá-lo da linha de comando.

## <a name="list-roles"></a>Listar funções

### <a name="list-role-definitions"></a>Lista de definições de função

Para listar todas as definições de função disponíveis, use [lista de definições de função az](/cli/azure/role/definition#az_role_definition_list):

```azurecli
az role definition list
```

O exemplo a seguir lista o nome e a descrição de todas as definições de função disponíveis:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
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
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description, "type":.properties.type}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "type": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>Relacionar ações de uma definição de função

Para listar as ações de uma definição de função, use [lista de definições de função az](/cli/azure/role/definition#az_role_definition_list):

```azurecli
az role definition list --name <role_name>
```

A exemplo a seguir lista a definição de função *Colaborador*:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "properties": {
      "additionalProperties": {
        "createdBy": null,
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedBy": null,
        "updatedOn": "2016-12-14T02:04:45.1393855Z"
      },
      "assignableScopes": [
        "/"
      ],
      "description": "Lets you manage everything except access to resources.",
      "permissions": [
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
      ],
      "roleName": "Contributor",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

A exemplo a seguir lista as *ações* e *não ações* da função *Colaborador*:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.properties.permissions[0].actions, "notActions":.properties.permissions[0].notActions}'
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
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .properties.permissions[0].actions'
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

### <a name="list-role-assignments-for-a-user"></a>Listar as atribuições de função de um usuário

Para listar as atribuições de função para um usuário específico, use a [lista de atribuições de função az](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --assignee <assignee>
```

Por padrão, somente as atribuições de escopo para a assinatura serão exibidas. Para exibir as atribuições de escopo por grupo ou recurso, use `--all`.

O exemplo a seguir lista as atribuições da função atribuídas diretamente ao usuário *patlong@contoso.com*:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.properties.principalName, "roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
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

Para listar as atribuições de função que existem para um grupo de recursos, use a [lista de atribuições de função az](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --resource-group <resource_group>
```

O exemplo a seguir lista as atribuições de função para o grupo de recursos *pharma-sales-projectforecast*:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
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

## <a name="assign-access"></a>Atribuir acesso

### <a name="assign-a-role-to-a-user"></a>Atribuir uma função a um usuário

Para atribuir uma função a um usuário no escopo do grupo de recursos, use [criar atribuição de função az](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

O exemplo a seguir atribui a função *Colaborador da Máquina Virtual* ao usuário *patlong@contoso.com* no escopo do grupo de recursos *pharma-sales-projectforcast*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="assign-a-role-to-a-group"></a>Atribuir uma função a um grupo

Para atribuir uma função a um grupo, use [criar atribuição de função az](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo a seguir atribui a função *Leitor* ao grupo *Ann Mack Team* com ID 22222222-2222-2222-2222-222222222222 no escopo da assinatura. Para obter a ID do grupo, você pode usar [lista de grupos do ad az](/cli/azure/ad/group#az_ad_group_list) ou [mostrar de grupo do ad az](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

O exemplo a seguir atribui a função *Colaborador da máquina Virtual* para o grupo *Ann Mack Team* com ID 22222222-2222-2222-2222-222222222222 em um escopo de recursos para uma rede virtual denominada *pharma-sales-project-network*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="assign-a-role-to-an-application"></a>Atribuir uma função a um aplicativo

Para atribuir uma função a um aplicativo, use [criar atribuição de função az](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

O exemplo a seguir atribui a função *Colaborador da Máquina Virtual* a um aplicativo com ID de objeto 44444444-4444-4444-4444-444444444444 no escopo do grupo de recursos *pharma-sales-projectforcast*. Para obter a ID de objeto do aplicativo, você pode usar [lista de aplicativos do ad az](/cli/azure/ad/app#az_ad_app_list) ou [mostrar de aplicativo do ad az](/cli/azure/ad/app#az_ad_app_show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Remover acesso

### <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

Para remover uma atribuição de função, use [excluir atribuição de função az](/cli/azure/role/assignment#az_role_assignment_delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

O exemplo a seguir remove a atribuição da função *Colaborador da Máquina Virtual* do usuário *patlong@contoso.com* no grupo de recursos *pharma-sales-projectforcast*:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

O exemplo a seguir exclui a função *Leitor* do grupo *Ann Mack Team* com ID 22222222-2222-2222-2222-222222222222 no escopo da assinatura. Para obter a ID do grupo, você pode usar [lista de grupos do ad az](/cli/azure/ad/group#az_ad_group_list) ou [mostrar de grupo do ad az](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>Funções personalizadas

### <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar as funções disponíveis para atribuição em um escopo, use a [lista de definições de função az](/cli/azure/role/definition#az_role_definition_list).

Ambos os exemplos a seguir listam todas as funções personalizadas na assinatura atual:

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.properties.roleName, "type":.properties.type}'
```

```azurecli
az role definition list --output json | jq '.[] | if .properties.type == "CustomRole" then {"roleName":.properties.roleName, "type":.properties.type} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar um arquivo personalizado, use [criar definição de função az](/cli/azure/role/definition#az_role_definition_create). A definição de função pode ser uma descrição de JSON ou um caminho para um arquivo que contém uma descrição de JSON.

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

### <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Para atualizar uma função personalizada, primeiro use [lista de definições de função az](/cli/azure/role/definition#az_role_definition_list) para recuperar a definição de função. Depois, faça as alterações desejadas na definição da função. Por fim, use [atualizar definição de função az](/cli/azure/role/definition#az_role_definition_update) para salvar a definição de função atualizada.

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

### <a name="delete-a-custom-role"></a>Excluir uma função personalizada

Para excluir um arquivo personalizado, use [excluir definição de função az](/cli/azure/role/definition#az_role_definition_delete). Para especificar a função a ser excluída, use o nome da função ou a ID de função. Para determinar a ID de função, use [lista de definições de função az](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition delete --name <role_name or role_id>
```

O exemplo a seguir exclui a função personalizada *Operador de Máquina Virtual*:

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

