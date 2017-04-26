---
title: "Grupos dinâmicos e Colaboração do Azure Active Directory B2B | Microsoft Docs"
description: "A colaboração B2B do Azure Active Directory pode ser usada com grupos dinâmicos do Azure AD"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/12/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9519719a63387cb8c341c72e262c2aeaacc3cd4a
ms.lasthandoff: 04/12/2017


---

# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Grupos dinâmicos e Colaboração do Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>O que são grupos dinâmicos?
A configuração dinâmica da associação de grupo de segurança para o Azure AD (Azure Active Directory) está disponível [no portal do Azure](https://portal.azure.com). Os administradores podem definir regras para preencher os grupos criados no Azure Active Directory com base nos atributos do usuário (como userType, departamento ou país). Isso permite que os membros sejam adicionados automaticamente ou removidos de um grupo de segurança com base nas alterações de seus atributos. Esses grupos podem ser usados para fornecer acesso a aplicativos ou a recursos de nuvem (como sites e documentos do SharePoint), e para atribuir licenças a membros. Leia mais sobre grupos dinâmicos em [Grupos dedicados no Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Agora, com uma assinatura Premium P1 ou P2 do AAD, o portal do Azure fornece a capacidade de criar regras avançadas para permitir associações dinâmicas mais complexas baseadas em atributos para grupos do Azure Active Directory. Saiba mais sobre como criar regras avançadas em [Usar atributos para criar regras avançadas para associação de grupo na visualização do Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>O que são os grupos dinâmicos internos?
O grupo dinâmico **Todos os usuários**permite que administradores de locatário criem um grupo contendo todos os usuários no locatário com um único clique. Por padrão, o grupo **Todos os usuários** inclui todos os usuários no diretório, incluindo Membros e Convidados.
No novo portal de administração do Azure Active Directory, você pode optar por habilitar o grupo **Todos os usuários** na exibição Configurações de Grupo.

![grupos internos](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

Proteção do grupo dinâmico Todos os usuários Por padrão, o grupo **Todos os usuários** contém também os usuários (convidados) da colaboração B2B. Se você quiser proteger ainda mais seu grupo **Todos os usuários** removendo os usuários convidados, modifique a regra baseada em atributos do grupo **Todos os usuários**. A ilustração a seguir mostra o grupo **Todos os usuários** modificado para excluir os convidados.

![habilitar o grupo todos os usuários](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

Talvez você também considere útil criar um novo grupo dinâmico que contém somente os usuários Convidados. Isso pode ser bastante útil para o direcionamento de políticas (como as políticas de Acesso Condicional) aos usuários Convidados.
Veja uma ilustração da aparência de um grupo como esse:

![excluir usuários convidados](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de usuário de colaboração B2B](active-directory-b2b-user-properties.md)
* [Adicionar um usuário de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Delegar convites de colaboração B2B](active-directory-b2b-delegate-invitations.md)
* [Código de colaboração B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicativos SaaS para colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuário de colaboração B2B](active-directory-b2b-user-token.md)
* [Mapeamento de declarações de usuário de colaboração B2B](active-directory-b2b-claims-mapping.md)
* [Compartilhamento externo do Office 365](active-directory-b2b-o365-external-user.md)
* [Limitações atuais da colaboração B2B](active-directory-b2b-current-limitations.md)

