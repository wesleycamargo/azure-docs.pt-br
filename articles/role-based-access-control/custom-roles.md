---
title: Criar funções personalizadas no RBAC do Azure | Microsoft Docs
description: Saiba como definir funções personalizadas com o Controle de Acesso Baseado em Função para um gerenciamento de identidade mais preciso na sua assinatura do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3baf616e448f1f6d5292161ae125502d72141940
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266587"
---
# <a name="create-custom-roles-in-azure"></a>Criar funções personalizadas no Azure

Se as [funções internas](built-in-roles.md) não atenderem às suas necessidades específicas de acesso, você pode criar suas próprias funções personalizadas. Assim como as funções internas, é possível atribuir funções personalizadas a usuários, grupos e entidades de serviço na assinatura, no grupo de recursos e nos escopos de recurso. As funções personalizadas são armazenadas em um locatário do Azure Active Directory (Azure AD) e podem ser compartilhadas entre assinaturas. Cada locatário pode ter até 2000 funções personalizadas. É possível criar funções personalizadas usando o Azure PowerShell, a CLI do Azure ou a API REST.

Este artigo descreve um exemplo de como começar a criar funções personalizadas usando o PowerShell e a CLI do Azure.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>Crie uma função personalizada para abrir as solicitações de suporte usando o PowerShell

Para criar uma função personalizada, você pode começar com uma função interna, editá-la e, em seguida, criar uma nova função. Neste exemplo, a função [Leitor](built-in-roles.md#reader) interno é personalizada para criar uma função personalizada chamada "Nível de acesso de tíquetes de suporte do leitor". Ele permite que o usuário veja tudo na assinatura e também as solicitações de suporte abertas.

> [!NOTE]
> Apenas duas funções internas que permitem que um usuário abra solicitações de suporte são [Proprietário](built-in-roles.md#owner) e [Colaborador](built-in-roles.md#contributor). Para um usuário poder abrir solicitações de suporte, ele deve receber uma função no escopo de assinatura, porque todas as solicitações de suporte são criadas com base em uma assinatura do Azure.

No PowerShell, use o comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para exportar a função [Leitor](built-in-roles.md#reader) no formato JSON.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

O exemplo a seguir mostra a saída JSON para a função [Leitor](built-in-roles.md#reader). Uma função típica é composta por três seções principais, `Actions`, `NotActions` e `AssignableScopes`. A seção `Actions` lista todas as operações permitidas para a função. Para excluir as operações de `Actions`, você as adiciona ao `NotActions`. As permissões efetivas são calculadas subtraindo as operações `NotActions` das operações `Actions`.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Em seguida, você pode editar a saída JSON para criar a função personalizada. Nesse caso, para criar tíquetes de suporte, a operação `Microsoft.Support/*` deve ser adicionada. Cada operação é disponibilizada por um provedor de recursos. Para obter uma lista de operações para um provedor de recursos, você pode usar o comando [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) ou consultar [Operações do provedor de recursos do Azure Resource Manager](resource-provider-operations.md).

É obrigatório que a função contenha as IDs de assinatura explícitos no local em que ela é usada. As IDs de assinatura são listadas sob `AssignableScopes`, caso contrário, você não poderá importar a função para sua assinatura.

Por fim, você deve definir a propriedade `IsCustom` para `true` para especificar que se trata de uma função personalizada.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Para criar a nova função personalizada, use o comando [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) e forneça o arquivo de definição da função JSON atualizado.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

Após executar [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition), a nova função personalizada está disponível no Portal do Azure e pode ser atribuída aos usuários.

![captura de tela de função personalizada importada no Portal do Azure](./media/custom-roles/18.png)

![captura de tela da atribuição de função personalizada importada ao usuário no mesmo diretório](./media/custom-roles/19.png)

![captura de tela de permissões para a função personalizada importada](./media/custom-roles/20.png)

Usuários com esta função personalizada podem criar novas solicitações de suporte.

![captura de tela de função personalizada criando solicitações de suporte](./media/custom-roles/21.png)

Usuários com esta função personalizada não podem executar outras ações, como criar VMs ou criar grupos de recursos.

![captura de tela de função personalizada que não pode criar VMs](./media/custom-roles/22.png)

![captura de tela de função personalizada que não pode criar novos RGs](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Crie uma função personalizada para abrir as solicitações de suporte usando a CLI do Azure

As etapas para criar uma função personalizada usando a CLI do Azure são semelhantes ao uso do PowerShell, exceto pela saída JSON que é diferente.

Neste exemplo, você pode iniciar com a função interna [Leitor](built-in-roles.md#reader). Para listar as ações da função [Leitor](built-in-roles.md#reader), use o comando [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Crie um arquivo JSON com o seguinte formato. A operação `Microsoft.Support/*` foi adicionada nas seções `Actions` para que esse usuário possa abrir solicitações de suporte enquanto continua sendo um leitor. Você precisa adicionar a ID de assinatura em que essa função será usada na seção `AssignableScopes`.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Para criar uma nova função personalizada, use o comando [az role definition create](/cli/azure/role/definition#az_role_definition_create).

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

A nova função personalizada agora está disponível no Portal do Azure e o processo para usar essa função é o mesmo que na seção PowerShell anterior.

![Captura de tela do Portal do Azure da função personalizada criada usando a CLI 1.0](./media/custom-roles/26.png)


## <a name="see-also"></a>Consulte também
- [Compreender as definições de função](role-definitions.md)
- [Gerenciar o controle de acesso baseado em função com o Azure PowerShell](role-assignments-powershell.md)
- [Gerenciar o controle de acesso baseado em função com a CLI do Azure](role-assignments-cli.md)
- [Gerenciar o controle de acesso baseado em função com a API REST](role-assignments-rest.md)
