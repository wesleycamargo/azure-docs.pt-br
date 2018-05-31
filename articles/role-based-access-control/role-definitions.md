---
title: Compreender as definições de função no RBAC do Azure | Microsoft Docs
description: Saiba mais sobre as definições RBAC (controle de acesso baseado em função) e como definir funções personalizadas para o gerenciamento de acesso refinado de recursos no Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161809"
---
# <a name="understand-role-definitions"></a>Compreender as definições de função

Se você estiver tentando compreender o funcionamento de uma função ou se estiver criando sua própria [função personalizada](custom-roles.md), é aconselhável entender como as funções são definidas. Este artigo descreve os detalhes das definições de função e fornece alguns exemplos.

## <a name="role-definition-structure"></a>Estrutura da definição da função

Uma *definição de função* é um conjunto de permissões. Às vezes, é chamada apenas de *função*. Uma definição de função lista as operações que podem ser executadas, como leitura, gravação e exclusão. Ela também pode listar as operações que não podem ser executadas. Uma definição de função tem a seguinte estrutura:

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

Operações são especificadas com cadeias de caracteres que têm o seguinte formato:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

A parte `{action}` de uma cadeia de caracteres de operação especifica o tipo de operações que você pode executar em um tipo de recurso. Por exemplo, você verá as seguintes subcadeias de caracteres em `{action}`:

| Subcadeia de caracteres de ação    | DESCRIÇÃO         |
| ------------------- | ------------------- |
| `*` | O caractere curinga concede acesso a todas as operações que correspondem à cadeia de caracteres. |
| `read` | Habilita operações de leitura (GET). |
| `write` | Habilita operações de gravação (PUT, POST e PATCH). |
| `delete` | Habilita operações de exclusão (DELETE). |

Aqui está a definição da função [Colaborador](built-in-roles.md#contributor) no formato JSON. A operação curinga (`*`) em `actions` indica que a entidade de segurança atribuída a essa função pode executar todas as ações ou, em outras palavras, pode gerenciar tudo. Isso inclui ações definidas no futuro, conforme o Azure adiciona novos tipos de recurso. As operações em `notActions` são subtraídas de `actions`. No caso da função [Contribuidor](built-in-roles.md#contributor), `notActions` remove a capacidade de essa função gerenciar o acesso a recursos e também atribuir acesso aos recursos.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>Operações de gerenciamento

O controle de acesso baseado em função para operações de gerenciamento é especificado nas seções `actions` e `notActions` de uma definição de função. Aqui estão alguns exemplos de operações de gerenciamento no Azure:

- Gerenciar o acesso a uma conta de armazenamento
- Criar, atualizar ou excluir um contêiner de blob
- Excluir um grupo de recursos e todos os seus recursos

O acesso de gerenciamento não é herdado para seus dados. Essa separação impede que funções com curingas (`*`) tenham acesso irrestrito aos seus dados. Por exemplo, se um usuário tiver uma função [Leitor](built-in-roles.md#reader) em uma assinatura, ele poderá exibir a conta de armazenamento, mas não os dados subjacentes.

## <a name="actions"></a>Ações

A permissão `actions` especifica as operações de gerenciamento às quais a função concede acesso. É uma coleção de cadeias de operação que identificam as operações protegíveis dos provedores de recursos do Azure. Aqui estão alguns exemplos de operações de gerenciamento que podem ser usadas no `actions`.

| Cadeia de caracteres da operação    | DESCRIÇÃO         |
| ------------------- | ------------------- |
| `*/read` | Concede acesso a operações de leitura a todos os tipos de recursos de todos os provedores de recursos do Azure.|
| `Microsoft.Compute/*` | Concede acesso a todas as operações a todos os tipos de recursos no provedor de recursos Microsoft.Compute.|
| `Microsoft.Network/*/read` | Concede acesso a operações de leitura a todos os tipos de recursos no provedor de recursos Microsoft.Network do Azure.|
| `Microsoft.Compute/virtualMachines/*` | Concede acesso a todas as operações de máquinas virtuais e seus tipos de recursos filho.|
| `microsoft.web/sites/restart/Action` | Concede acesso para reinicialização de um aplicativo Web.|

## <a name="notactions"></a>notActions

A permissão `notActions` especifica as operações de gerenciamento que são excluídas do `actions` permitido. Use a permissão `notActions` se o conjunto de operações que você deseja permitir fica definido mais facilmente excluindo as operações restritas. O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as operações `notActions` das operações `actions`.

> [!NOTE]
> Se um usuário for atribuído a uma função que exclui uma operação em `notActions` e for atribuído a uma segunda função que concede acesso à mesma operação, ele terá permissão para executar essa operação. `notActions` não é uma regra de negação, é simplesmente uma maneira conveniente de criar um conjunto de operações permitidas quando for necessário excluir operações específicas.
>

## <a name="assignablescopes"></a>assignableScopes

A seção `assignableScopes` especifica os escopos (grupos de gerenciamento (atualmente na versão prévia), assinaturas, grupos de recursos ou recursos) em que a função está disponível para atribuição. Você pode disponibilizar a função para atribuição apenas nas assinaturas ou grupos de recursos que a exijam e não sobrecarregar a experiência do usuário nas assinaturas ou grupos de recursos restantes. Você deve usar pelo menos um grupo de gerenciamento, uma assinatura, um grupo de recursos ou uma ID de recurso.

As funções internas têm `assignableScopes` definido como o escopo raiz (`"/"`). O escopo raiz indica que a função está disponível para atribuição em todos os escopos. Você não pode usar o escopo raiz em suas próprias funções personalizadas. Se você tentar, receberá um erro de autorização.

Exemplos de escopos válidos que podem ser atribuídos incluem:

| Cenário | Exemplo |
|----------|---------|
| A função está disponível para atribuição em uma assinatura única | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| A função está disponível para atribuição em duas assinaturas | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| A função está disponível para atribuição apenas no grupo de recursos de rede | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| A função está disponível para atribuição em todos os escopos | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes e funções personalizadas

A seção `assignableScopes` de uma função personalizada também controla quem pode criar, excluir, modificar ou exibir a função personalizada.

| Tarefa | Operação | DESCRIÇÃO |
| --- | --- | --- |
| Criar/excluir uma função personalizada | `Microsoft.Authorization/ roleDefinition/write` | Os usuários que recebem essa operação em todos os `assignableScopes` da função personalizada podem criar (ou excluir) funções personalizadas para uso nesses escopos. Por exemplo, os [Proprietários](built-in-roles.md#owner) e os [Administradores de Acesso do Usuário](built-in-roles.md#user-access-administrator) das assinaturas, grupos de recursos e recursos. |
| Modificar uma função personalizada | `Microsoft.Authorization/ roleDefinition/write` | Os usuários que recebem essa operação em todos os `assignableScopes` da função personalizada podem modificar as funções personalizadas nesses escopos. Por exemplo, os [Proprietários](built-in-roles.md#owner) e os [Administradores de Acesso do Usuário](built-in-roles.md#user-access-administrator) das assinaturas, grupos de recursos e recursos. |
| Exibir uma função personalizada | `Microsoft.Authorization/ roleDefinition/read` | Os usuários que recebem essa operação em um escopo podem exibir as funções personalizadas que estão disponíveis para atribuição nesse escopo. Todas as funções internas permitem que funções personalizadas estejam disponíveis para atribuição. |

## <a name="role-definition-examples"></a>Exemplos de definição de função

A exemplo a seguir mostra a definição da função [Leitor](built-in-roles.md#reader) conforme exibido usando a CLI do Azure:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

O exemplo a seguir mostra uma função personalizada para monitoramento e reinicialização de máquinas virtuais conforme mostrado usando o Azure PowerShell:

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
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Consulte também

* [Funções internas](built-in-roles.md)
* [Funções personalizadas](custom-roles.md)
* [Operações do provedor de recursos do Azure Resource Manager](resource-provider-operations.md)
