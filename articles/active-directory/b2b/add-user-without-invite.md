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
ms.openlocfilehash: 589d9a417dae5c40d24d25c4ef864ce903fbfbe3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259451"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Adicionar usuários convidados da colaboração B2B sem um convite

> [!NOTE]
> Agora, usuários convidados não precisam mais do e-mail de convite, exceto em alguns casos especiais. Para obter mais informações, consulte [Resgate do convite de colaboração B2B](redemption-experience.md).  

É possível permitir que um usuário, como um representante do parceiro, adicione usuários do parceiro à organização sem a necessidade de resgatar convites. Basta conceder a esse usuário privilégios de enumeração no diretório que está sendo usado para a organização do parceiro. 

Conceda esses privilégios quando:

1. Um usuário na organização host (por exemplo, WoodGrove) convida um usuário da organização parceira (por exemplo, Sam@litware.com) como Convidado.
2. O administrador da organização host [define as políticas](delegate-invitations.md) que permitem ao Sam identificar e adicionar outros usuários da organização parceira (Litware).
3. Agora, Davi pode adicionar outros usuários da Litware ao diretório, grupos ou aplicativos da WoodGrove sem a necessidade de resgatar os convites. Se Davi tiver os privilégios apropriados de enumeração na Litware, isso ocorrerá automaticamente.

### <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B?](add-users-information-worker.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Delegar convites para a colaboração do Azure Active Directory B2B](delegate-invitations.md)

