---
title: Listar atribuições de negação usando RBAC e API REST – Azure | Microsoft Docs
description: Saiba como listar atribuições de negação para usuários, grupos e aplicativos usando RBAC (controle de acesso baseado em função) e API REST.
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
ms.date: 09/24/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44c1d3b18bb9bdc63247379fe3f277cb6542f2da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975574"
---
# <a name="list-deny-assignments-using-rbac-and-the-rest-api"></a>Listar atribuições de negação usando RBAC e a API REST

Atualmente as atribuições de negação são **somente leitura** e só podem ser definidas pelo Azure. Mesmo que você não possa criar suas próprias atribuições de negação, é possível listar as atribuições de negação, porque elas poderiam afetar suas permissões efetivas. Este artigo descreve como listar atribuições de negação usando RBAC e API REST.

## <a name="list-a-single-deny-assignment"></a>Lista uma única atribuição de negação

1. Comece com a solicitação a seguir:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para o qual você deseja listar as atribuições de negação.

    | Escopo | Tipo |
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

    | Escopo | Tipo |
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

## <a name="next-steps"></a>Próximas etapas

- [Compreender atribuições de negação](deny-assignments.md)
- [Elevar o acesso de um Administrador Global no Azure Active Directory](elevate-access-global-admin.md)
- [Referência de API REST do Azure](/rest/api/azure/)
