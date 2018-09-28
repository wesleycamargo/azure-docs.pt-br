---
title: Compreender atribuições de negação no RBAC do Azure | Microsoft Docs
description: Saiba mais sobre atribuições de negação no RBAC (controle de acesso baseado em função) para recursos no Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 8ef3a2ec44c5eff80d3a50a6c56805667e164ba8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980161"
---
# <a name="understand-deny-assignments"></a>Compreender atribuições de negação

Semelhante a uma atribuição de função, uma *atribuição de negação* associa um conjunto de ações de negação a um usuário, grupo ou entidade de serviço em um escopo específico com a finalidade de negar acesso. Uma atribuição de negação também pode excluir entidades de segurança e impedir a herança para escopos filho, o que é diferente do que ocorre com as atribuições de função. Atualmente as atribuições de negação são **somente leitura** e só podem ser definidas pelo Azure. Este artigo descreve como as atribuições de negação são definidas.

## <a name="deny-assignment-properties"></a>Propriedades de atribuição de negação

 Uma atribuição de negação tem as seguintes propriedades:

> [!div class="mx-tableFixed"]
> | Propriedade | Obrigatório | Tipo | DESCRIÇÃO |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | SIM | Cadeia de caracteres | O nome de exibição da atribuição de negação. Os nomes devem ser exclusivos para um determinado escopo. |
> | `Description` | Não  | Cadeia de caracteres | A descrição da atribuição de negação. |
> | `Permissions.Actions` | Pelo menos um Actions ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gerenciamento para as quais o acesso é bloqueado pela atribuição de negação. |
> | `Permissions.NotActions` | Não  | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gerenciamento a excluir da atribuição de negação. |
> | `Permissions.DataActions` | Pelo menos um Actions ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados para as quais o acesso é bloqueado pela atribuição de negação. |
> | `Permissions.NotDataActions` | Não  | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados a excluir da atribuição de negação. |
> | `Scope` | Não  | Cadeia de caracteres | Uma cadeia de caracteres que especifica o escopo ao qual a atribuição de negação se aplica. |
> | `DoNotApplyToChildScopes` | Não  | BOOLEAN | Especifica se a atribuição de negação se aplica a escopos filho. O valor padrão é falso. |
> | `Principals[i].Id` | SIM | String[] | Uma matriz de IDs de objeto de entidade de segurança do Azure AD (usuário, grupo ou entidade de serviço) ao qual a atribuição de negação se aplica. Definido como um GUID vazio `00000000-0000-0000-0000-000000000000` para representar todas as pessoas. |
> | `Principals[i].Type` | Não  | String[] | Uma matriz de tipos de objeto representados por Principals[i].Id. Definido como `Everyone` para representar todas as pessoas. |
> | `ExcludePrincipals[i].Id` | Não  | String[] | Uma matriz de IDs de objeto de entidade de segurança do Azure AD (usuário, grupo ou entidade de serviço) ao qual a atribuição de negação não se aplica. |
> | `ExcludePrincipals[i].Type` | Não  | String[] | Uma matriz de tipos de objeto representados por ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Não  | BOOLEAN | Especifica se esta atribuição de negação foi ou não criada pelo Azure e não pode ser editada ou excluída. Atualmente, todas as atribuições de negação são protegidas pelo sistema. |

## <a name="everyone-principal"></a>Entidade de segurança everyone

Para negar o suporte a atribuições, a entidade de segurança everyone foi introduzida. A entidade de segurança everyone representa todos os usuários, grupos e entidades de serviço em um diretório do Azure AD. Se a ID da entidade de segurança é um GUID de zeros (`00000000-0000-0000-0000-000000000000`) e o tipo de entidade de segurança é `Everyone`, a entidade de segurança representa todas as pessoas. A entidade de segurança everyone pode ser combinada com `ExcludePrincipals` para negar todos, exceto alguns usuários. A entidade de segurança everyone tem as seguintes restrições:

- Pode ser usada apenas em `Principals` e não pode ser usada em `ExcludePrincipals`.
- `Principals[i].Type` deve ser definido como `Everyone`.

## <a name="next-steps"></a>Próximas etapas

* [Listar atribuições de negação usando RBAC e a API REST](deny-assignments-rest.md)
* [Compreender as definições de função](role-definitions.md)
