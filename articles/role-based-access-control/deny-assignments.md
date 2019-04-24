---
title: Compreender atribuições de negação dos recursos do Azure | Microsoft Docs
description: Saiba mais sobre atribuições de negação no RBAC (controle de acesso baseado em função) para recursos do Azure.
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
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 497571a65510f806d7d7994c9dc37f9a00b65a5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197129"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>Compreender atribuições de negação dos recursos do Azure

Semelhante a uma atribuição de função, uma *negação de atribuição* anexa um conjunto de ações de negação a um usuário, grupo ou entidade de serviço em um escopo específico com o objetivo de negar acesso. As atribuições de negação impedem que os usuários executem ações específicas, mesmo que uma atribuição de função conceda o acesso a eles. Alguns provedores de recursos no Azure agora incluem designações de negação.

De certa forma, as designações de negação são diferentes das designações de função. Negar atribuições pode excluir entidades e impedir a herança para escopos filho. Negar atribuições também se aplicam a [atribuições do administrador de assinatura clássico](rbac-and-directory-admin-roles.md).

Este artigo descreve como as atribuições de negação são definidas.

> [!NOTE]
> Neste momento, a única maneira de adicionar suas próprias atribuições de negação é usando o Azure Blueprints. Para obter mais informações, consulte [Proteger novos recursos com bloqueios de recurso do Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="deny-assignment-properties"></a>Propriedades de atribuição de negação

 Uma atribuição de negação tem as seguintes propriedades:

> [!div class="mx-tableFixed"]
> | Propriedade | Necessário | Type | DESCRIÇÃO |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Sim | Cadeia de caracteres | O nome de exibição da atribuição de negação. Os nomes devem ser exclusivos para um determinado escopo. |
> | `Description` | Não  | Cadeia de caracteres | A descrição da atribuição de negação. |
> | `Permissions.Actions` | Pelo menos um Actions ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gerenciamento para as quais o acesso é bloqueado pela atribuição de negação. |
> | `Permissions.NotActions` | Não  | String[] | Uma matriz de cadeias de caracteres que especificam as operações de gerenciamento a excluir da atribuição de negação. |
> | `Permissions.DataActions` | Pelo menos um Actions ou um DataActions | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados para as quais o acesso é bloqueado pela atribuição de negação. |
> | `Permissions.NotDataActions` | Não  | String[] | Uma matriz de cadeias de caracteres que especificam as operações de dados a excluir da atribuição de negação. |
> | `Scope` | Não  | Cadeia de caracteres | Uma cadeia de caracteres que especifica o escopo ao qual a atribuição de negação se aplica. |
> | `DoNotApplyToChildScopes` | Não  | Boolean | Especifica se a atribuição de negação se aplica a escopos filho. O valor padrão é falso. |
> | `Principals[i].Id` | Sim | String[] | Uma matriz de IDs de objeto principal do Microsoft Azure Active Directory (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação se aplica. Defina como um GUID vazio `00000000-0000-0000-0000-000000000000` para representar todos os principais. |
> | `Principals[i].Type` | Não  | String[] | Uma matriz de tipos de objeto representados por Principals[i].Id. Defina como `SystemDefined` para representar todos os principais. |
> | `ExcludePrincipals[i].Id` | Não  | String[] | Uma matriz de IDs de objeto principal do Microsoft Azure Active Directory (usuário, grupo, entidade de serviço ou identidade gerenciada) à qual a atribuição de negação não se aplica. |
> | `ExcludePrincipals[i].Type` | Não  | String[] | Uma matriz de tipos de objeto representados por ExcludePrincipals[i].Id. |
> | `IsSystemProtected` | Não  | Boolean | Especifica se esta atribuição de negação foi ou não criada pelo Azure e não pode ser editada ou excluída. Atualmente, todas as atribuições de negação são protegidas pelo sistema. |

## <a name="system-defined-principal"></a>Entidade definida pelo sistema

Para suportar negar atribuições, o **Principal definido pelo sistema** foi introduzido. Essa entidade representa todos os usuários, grupos, entidades de serviço e identidades gerenciadas em um diretório do Microsoft Azure Active Directory. Se o ID principal for um zero GUID `00000000-0000-0000-0000-000000000000` e o tipo principal for `SystemDefined`, o principal representará todos os principais. `SystemDefined` pode ser combinado com `ExcludePrincipals` para negar todos os principais, exceto alguns usuários. `SystemDefined` tem as seguintes restrições:

- Pode ser usada apenas em `Principals` e não pode ser usada em `ExcludePrincipals`.
- `Principals[i].Type` deve ser definido como `SystemDefined`.

## <a name="next-steps"></a>Próximas etapas

* [Modo de exibição negar as atribuições de recursos do Azure usando o portal do Azure](deny-assignments-portal.md)
* [Noções básicas sobre definições de função para recursos do Azure](role-definitions.md)
