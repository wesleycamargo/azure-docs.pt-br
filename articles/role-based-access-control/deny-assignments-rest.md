---
title: Listar atribuições de negação para recursos do Azure usando a API REST – Microsoft Azure | Microsoft Docs
description: Saiba como listar atribuições de negação para usuários, grupos e aplicativos usando RBAC (controle de acesso baseado em função) para recursos do Azure e API REST.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 59bcf2b33d203ae216b4965b963a727a6b34ae72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194660"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-rest-api"></a>Listar atribuições de negação para recursos do Azure usando a API REST

As [atribuições de negação](deny-assignments.md) impedem que os usuários executem ações específicas, mesmo que uma atribuição de função conceda o acesso a elas. Este artigo descreve como usar a API REST para a lista de atribuições de negar.

> [!NOTE]
> Neste momento, a única maneira de adicionar suas próprias atribuições de negação é usando o Azure Blueprints. Para obter mais informações, consulte [Proteger novos recursos com bloqueios de recurso do Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md).

## <a name="prerequisites"></a>Pré-requisitos

Para obter informações sobre uma atribuição de deny, você deve ter:

- `Microsoft.Authorization/denyAssignments/read` permissão, que está incluído na maioria [funções internas para recursos do Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Lista uma única atribuição de negação

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as atribuições de negação.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{deny-assignment-id}* pelo identificador de atribuição de negação que você deseja recuperar.

## <a name="list-multiple-deny-assignments"></a>Listar várias atribuições de negação

1. Inicie com uma das seguintes solicitações:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Com parâmetros opcionais:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as atribuições de negação.

    | Escopo | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Assinatura |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupo de recursos |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Recurso |

1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de atribuição de negação.

    | Filter | DESCRIÇÃO |
    | --- | --- |
    | (sem filtro) | Listar todas as atribuições de negação em, acima e abaixo do escopo especificado. |
    | `$filter=atScope()` | Listar as atribuições de negação apenas para o escopo especificado e acima. Não inclui as atribuições de negação em sub-escopos. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Listar atribuições de negação com o nome especificado. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Listar atribuições de negação no escopo raiz (/)

1. Elevar o acesso conforme descrito em [Elevar acesso para um Administrador Global no Azure Active Directory](elevate-access-global-admin.md).

1. Envie a seguinte solicitação:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Substitua *{filter}* pela condição que você deseja aplicar a fim de filtrar a lista de atribuição de negação. Um filtro é exigido.

    | Filter | DESCRIÇÃO |
    | --- | --- |
    | `$filter=atScope()` | Listar atribuições de negação apenas no escopo raiz. Não inclui as atribuições de negação em sub-escopos. |
    | `$filter=denyAssignmentName%20eq%20'{deny-assignment-name}'` | Listar atribuições de negação com o nome especificado. |

1. Remover acesso elevado.

## <a name="next-steps"></a>Próximos passos

- [Compreender atribuições de negação dos recursos do Azure](deny-assignments.md)
- [Elevar o acesso de um Administrador Global no Azure Active Directory](elevate-access-global-admin.md)
- [Referência de API REST do Azure](/rest/api/azure/)
