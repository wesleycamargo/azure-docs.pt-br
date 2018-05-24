---
title: Adicionar usuários de colaboração B2B ao Azure Active Directory sem um convite | Microsoft Docs
description: É possível permitir que um usuário convidado adicione outros usuários convidados ao Azure AD sem resgatar um convite na colaboração B2B do Azure Active Directory.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075616"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Adicionar usuários convidados da colaboração B2B sem um convite

> [!NOTE]
> Agora, usuários convidados não precisam mais do e-mail de convite, exceto em alguns casos especiais. Para obter mais informações, consulte [Resgate do convite de colaboração B2B](active-directory-b2b-redemption-experience.md).  

É possível permitir que um usuário, como um representante do parceiro, adicione usuários do parceiro à organização sem a necessidade de resgatar convites. Basta conceder a esse usuário privilégios de enumeração no diretório que está sendo usado para a organização do parceiro. 

Conceda esses privilégios quando:

1. Um usuário na organização host (por exemplo, WoodGrove) convida um usuário da organização parceira (por exemplo, Sam@litware.com) como Convidado.
2. O administrador da organização host [define as políticas](active-directory-b2b-delegate-invitations.md) que permitem ao Sam identificar e adicionar outros usuários da organização parceira (Litware).
3. Agora, Davi pode adicionar outros usuários da Litware ao diretório, grupos ou aplicativos da WoodGrove sem a necessidade de resgatar os convites. Se Davi tiver os privilégios apropriados de enumeração na Litware, isso ocorrerá automaticamente.

### <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
- [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
- [Delegar convites para a colaboração do Azure Active Directory B2B](active-directory-b2b-delegate-invitations.md)

