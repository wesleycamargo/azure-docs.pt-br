---
title: Grupos dinâmicos e colaboração B2B – Azure Active Directory | Microsoft Docs
description: Mostra como usar os grupos dinâmicos do Azure AD com a colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066c3cc0a722ea96ff98aba613b493e2be77df0e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668469"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinâmicos e Colaboração do Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>O que são grupos dinâmicos?
A configuração dinâmica da associação de grupo de segurança para o Azure AD (Azure Active Directory) está disponível [no portal do Azure](https://portal.azure.com). Os administradores podem definir regras para preencher os grupos criados no Azure AD com base nos atributos do usuário (como userType, departamento ou país). Os membros podem ser adicionados ou removidos automaticamente de um grupo de segurança com base nas alterações de seus atributos. Esses grupos podem fornecer acesso a aplicativos ou a recursos de nuvem (como sites e documentos do SharePoint) e para atribuir licenças a membros. Leia mais sobre grupos dinâmicos em [Grupos dedicados no Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

O [licenciamento do Azure AD Premium P1 ou P2](https://azure.microsoft.com/pricing/details/active-directory/) apropriado é necessário para criar e usar grupos dinâmicos. Saiba mais no artigo [Criar regras baseadas em atributo para associação dinâmica de grupo no Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>O que são os grupos dinâmicos internos?
O grupo dinâmico **Todos os usuários**permite que administradores de locatário criem um grupo contendo todos os usuários no locatário com um único clique. Por padrão, o grupo **Todos os usuários** inclui todos os usuários no diretório, incluindo Membros e Convidados.
No novo portal de administração do Azure Active Directory, você pode optar por habilitar o grupo **Todos os usuários** na exibição Configurações de Grupo.

![Mostra habilitar o grupo Todos os Usuários definido como Sim](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Fortalecendo a proteção do grupo dinâmico Todos os usuários
Por padrão, o grupo **Todos os usuários** também contém os usuários de colaboração B2B (convidados). Você pode proteger ainda mais o grupo **Todos os usuários** usando uma regra para remover os usuários convidados. A ilustração a seguir mostra o grupo **Todos os usuários** modificado para excluir os convidados.

![Mostra a regra onde o tipo de usuário não é igual a convidado](media/use-dynamic-groups/exclude-guest-users.png)

Também pode ser útil criar um novo grupo dinâmico que contenha apenas os usuários convidados, para que você possa aplicar políticas (como as políticas de acesso condicional do Azure AD) a eles.
Como esse grupo poderia ser:

![Mostra a regra onde o tipo de usuário é igual a convidado](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Próximas etapas

- [Propriedades de usuário de colaboração B2B](user-properties.md)
- [Adicionar um usuário de colaboração B2B a uma função](add-guest-to-role.md)
- [Acesso condicional para usuários de colaboração B2B](conditional-access.md)

