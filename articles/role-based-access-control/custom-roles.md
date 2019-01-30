---
title: Funções personalizadas no Azure | Microsoft Docs
description: Saiba como definir as funções personalizadas com RBAC (controle de acesso baseado em função) do Azure para o gerenciamento de acesso refinado de recursos no Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 17a2d1ac747b46ed9a55ceffeea3ba9f4b2f0bc7
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412041"
---
# <a name="custom-roles-in-azure"></a>Funções personalizadas no Azure

Se as [funções internas](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar suas próprias funções personalizadas. Assim como as funções internas, é possível atribuir funções personalizadas a usuários, grupos e entidades de serviço na assinatura, no grupo de recursos e nos escopos de recurso. As funções personalizadas são armazenadas em um diretório do Azure AD (Azure Active Directory) e podem ser compartilhadas entre assinaturas. Cada diretório pode ter até 2 mil funções personalizadas. É possível criar funções personalizadas usando o Azure PowerShell, a CLI do Azure ou a API REST.

## <a name="custom-role-example"></a>Exemplo de função personalizada

A seguir, mostra como uma função personalizada se parece, conforme exibida no formato JSON. Essa função personalizada pode ser usada para monitorar e reiniciar máquinas virtuais.

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
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
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

Quando você cria uma função personalizada, ela aparece no portal do Azure com um ícone de recurso laranja.

![Ícone personalizado da função](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Etapas para criar uma função personalizada

1. Determinar as permissões necessárias

    Ao criar uma função personalizada, você precisa conhecer as operações do provedor de recursos que estão disponíveis para definir suas permissões. Para exibir a lista de operações, você pode usar os comandos [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) ou [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).
    Para especificar as permissões para a função personalizada, você adiciona as operações para as propriedades `Actions` ou `NotActions` da [definição de função](role-definitions.md). Se você tiver operações de dados, adicione-as às propriedades `DataActions` ou `NotDataActions`.

2. Criar a função personalizada

    Você pode usar o Azure PowerShell ou a CLI do Azure para criar a função personalizada. Normalmente, você começa com uma função interna existente e depois a modifica conforme suas necessidades. Depois, use os comandos [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) ou [az role definition create](/cli/azure/role/definition#az-role-definition-create) para criar a função personalizada. Para criar uma função personalizada, você precisa ter a permissão `Microsoft.Authorization/roleDefinitions/write` em todo `AssignableScopes`, como [Proprietário](built-in-roles.md#owner) ou [Administrador de Acesso de Usuário](built-in-roles.md#user-access-administrator).

3. Testar a função personalizada

    Quando tiver sua função personalizada, será necessário testá-la para verificar se funciona como o esperado. Se você precisar fazer ajustes mais tarde, você pode atualizar a função personalizada.

Para obter um tutorial passo a passo sobre como criar uma função personalizada, confira [Tutorial: Criar uma função personalizada usando o Azure PowerShell](tutorial-custom-role-powershell.md) ou [Tutorial: Criar uma função personalizada usando a CLI do Azure](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Propriedades da função personalizada

Uma função personalizada tem as seguintes propriedades.

| Propriedade | Obrigatório | Tipo | DESCRIÇÃO |
| --- | --- | --- | --- |
| `Name` | SIM | Cadeia de caracteres | O nome de exibição da função personalizada. Embora a definição de função seja um recurso no nível da assinatura, ela pode ser usada em várias assinaturas que compartilham o mesmo diretório do Azure AD. Esse nome de exibição precisa ser exclusivo no escopo do diretório do Azure AD. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é 128. |
| `Id` | SIM | Cadeia de caracteres | A ID exclusiva da função personalizada. Para o Azure PowerShell e a CLI do Azure, essa ID é gerada automaticamente ao criar uma nova função. |
| `IsCustom` | SIM | Cadeia de caracteres | Indica se esta é uma função personalizada. Defina como `true` para funções personalizadas. |
| `Description` | SIM | Cadeia de caracteres | A descrição da função personalizada. Pode incluir letras, números, espaços e caracteres especiais. O número máximo de caracteres é 1024. |
| `Actions` | SIM | String[] | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento permitidas pela função. Para obter mais informações, consulte [Ações](role-definitions.md#actions). |
| `NotActions` | Não  | String[] | Uma matriz de cadeias de caracteres que especifica as operações de gerenciamento que são excluídas do `Actions` permitido. Para obter mais informações, consulte [NotActions](role-definitions.md#notactions). |
| `DataActions` | Não  | String[] | Uma matriz de cadeias de caracteres que especifica as operações de dados permitidas pela função em seus dados dentro desse objeto. Para obter mais informações, consulte [DataActions (versão prévia)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Não  | String[] | Uma matriz de cadeias de caracteres que especifica as operações de dados excluídas do `DataActions` permitido. Para obter mais informações, consulte [NotDataActions (versão prévia)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | SIM | String[] | Uma matriz de cadeias de caracteres que especifica os escopos para os quais a função personalizada está disponível para atribuição. Atualmente não pode ser definido para o escopo raiz (`"/"`) ou um escopo de grupo de gerenciamento. Para obter mais informações, consulte [AssignableScopes](role-definitions.md#assignablescopes) e [Organize seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Quem pode criar, excluir, atualizar ou exibir uma função personalizada

Assim como funções internas, a propriedade `AssignableScopes` especifica os escopos para os quais a função está disponível para atribuição. A propriedade `AssignableScopes` de uma função personalizada também controla quem pode criar, excluir, atualizar ou exibir a função personalizada.

| Tarefa | Operação | DESCRIÇÃO |
| --- | --- | --- |
| Criar/excluir uma função personalizada | `Microsoft.Authorization/ roleDefinition/write` | Os usuários que recebem essa operação em todos os `AssignableScopes` da função personalizada podem criar (ou excluir) funções personalizadas para uso nesses escopos. Por exemplo, os [Proprietários](built-in-roles.md#owner) e os [Administradores de Acesso do Usuário](built-in-roles.md#user-access-administrator) das assinaturas, grupos de recursos e recursos. |
| Atualizar uma função personalizada | `Microsoft.Authorization/ roleDefinition/write` | Os usuários que recebem essa operação em todos os `AssignableScopes` da função personalizada podem atualizar as funções personalizadas nesses escopos. Por exemplo, os [Proprietários](built-in-roles.md#owner) e os [Administradores de Acesso do Usuário](built-in-roles.md#user-access-administrator) das assinaturas, grupos de recursos e recursos. |
| Exibir uma função personalizada | `Microsoft.Authorization/ roleDefinition/read` | Os usuários que recebem essa operação em um escopo podem exibir as funções personalizadas que estão disponíveis para atribuição nesse escopo. Todas as funções internas permitem que funções personalizadas estejam disponíveis para atribuição. |

## <a name="next-steps"></a>Próximas etapas
- [Criar funções personalizadas usando o Azure PowerShell](custom-roles-powershell.md)
- [Criar funções personalizadas usando a CLI do Azure](custom-roles-cli.md)
- [Compreender as definições de função](role-definitions.md)
- [Solução de problemas do RBAC no Azure](troubleshooting.md)
