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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: fa1a979c01999bd79c45d24e4c7771edaf346dd8
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52632408"
---
# <a name="understand-deny-assignments"></a>Compreender atribuições de negação

Semelhante a uma atribuição de função, uma *negação de atribuição* anexa um conjunto de ações de negação a um usuário, grupo ou entidade de serviço em um escopo específico com o objetivo de negar acesso. As designações de negação impedem que os usuários executem ações específicas, mesmo que uma atribuição de função conceda a eles acesso. Alguns provedores de recursos no Azure agora incluem designações de negação. Atualmente as atribuições de negação são **somente leitura** e só podem ser definidas pelo Azure.

De certa forma, as designações de negação são diferentes das designações de função. Negar atribuições pode excluir entidades e impedir a herança para escopos filho. Negar atribuições também se aplicam a [atribuições do administrador de assinatura clássico](rbac-and-directory-admin-roles.md).

Este artigo descreve como as atribuições de negação são definidas.

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
> | `Principals[i].Id` | SIM | String[] | Uma matriz de IDs de objeto principal do Microsoft Azure Active Directory (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação se aplica. Defina como um GUID vazio `00000000-0000-0000-0000-000000000000` para representar todos os principais. |
> | `Principals[i].Type` | Não  | String[] | Uma matriz de tipos de objeto representados por Principals[i].Id. Defina como `SystemDefined` para representar todos os principais. |
> | `ExcludePrincipals[i].Id` | Não  | String[] | Uma matriz de IDs de objeto principal do Microsoft Azure Active Directory (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação não se aplica. |
> | `ExcludePrincipals[i].Type` | Não  | String[] | Uma matriz de tipos de objeto representados por ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Não  | BOOLEAN | Especifica se esta atribuição de negação foi ou não criada pelo Azure e não pode ser editada ou excluída. Atualmente, todas as atribuições de negação são protegidas pelo sistema. |

## <a name="system-defined-principal"></a>Entidade definida pelo sistema

Para suportar negar atribuições, o **Principal definido pelo sistema** foi introduzido. Essa entidade representa todos os usuários, grupos, entidades de serviço e identidades gerenciadas em um diretório do Microsoft Azure Active Directory. Se o ID principal for um zero GUID `00000000-0000-0000-0000-000000000000` e o tipo principal for `SystemDefined`, o principal representará todos os principais. `SystemDefined` pode ser combinado com `ExcludePrincipals` para negar todos os principais, exceto alguns usuários. `SystemDefined` tem as seguintes restrições:

- Pode ser usada apenas em `Principals` e não pode ser usada em `ExcludePrincipals`.
- `Principals[i].Type` deve ser definido como `SystemDefined`.

## <a name="next-steps"></a>Próximas etapas

* [Listar atribuições de negação usando RBAC e a API REST](deny-assignments-rest.md)
* [Compreender as definições de função](role-definitions.md)
