---

title: "Adicionar usuários de colaboração B2B ao Azure Active Directory sem um convite | Microsoft Docs"
description: "A colaboração B2B do Azure Active Directory permite que os operadores de informações adicionem usuários da respectiva organização ao Azure AD para acessar seus aplicativos corporativos"
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
ms.date: 02/10/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0eef684115f9c21ea61502a10576f74cac0ace8e
ms.openlocfilehash: 8fa8ba169ca85c33e52eee4a7cd5b84aa4012673


---

# <a name="add-b2b-collaboration-users-without-an-invitation"></a>Adicionar usuários de colaboração B2B sem um convite

Se o emissor do convite pertencer a uma função que tem privilégios de enumeração no diretório da organização parceira, do qual ele está adicionando usuários, os usuários convidados serão adicionados à organização que convida sem a necessidade de convites.

Este é o cenário no qual isso é mais útil:

1. Um usuário na organização host (por exemplo, WoodGrove) convida um usuário da organização parceira (por exemplo, Sam@litware.com) como Convidado.
2. O administrador da organização host define as políticas que permitem ao Sam identificar e adicionar outros usuários da organização parceira (Litware).
3. Agora, Sam pode adicionar outros usuários da Litware ao diretório, grupos ou aplicativos da WoodGrove sem a necessidade de resgate de convites. Se Sam tiver os privilégios apropriados de enumeração na Litware, isso ocorrerá automaticamente.

### <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Perguntas frequentes sobre colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalização da colaboração B2B do Azure Active Directory](active-directory-b2b-api.md)
* [Autenticação Multifator para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


