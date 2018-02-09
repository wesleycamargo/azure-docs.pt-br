---
title: "Colaboração B2B do Azure Active Directory para organizações híbridas| Microsoft Docs"
description: "Fornecer aos parceiros acesso a ambos os recursos locais e da nuvem com a colaboração B2B do Azure Active Directory"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Colaboração B2B do Azure Active Directory para organizações híbridas

Em uma organização híbrida, em que tem ambos os recursos locais e da nuvem, talvez você queira dar aos parceiros externos acesso aos recursos locais e aos da nuvem. Para o acesso aos recursos locais, você pode gerenciar as contas do parceiro localmente no seu ambiente local do Active Directory. Os parceiros entram com as credenciais de conta do parceiro para acessar os recursos da sua organização. Para fornecer aos parceiros acesso aos recursos da nuvem usando essas mesmas credenciais, agora você pode usar o Azure Active Directory (Azure AD) Connect para sincronizar as contas de parceiro com a nuvem, como usuários B2B do Azure (ou seja, os usuários com UserType = Convidado).

## <a name="identify-unique-attributes-for-usertype"></a>Identificar os atributos exclusivos para UserType

Antes de habilitar a sincronização do atributo UserType, primeiro você deve decidir como o atributo UserType será derivado do Active Directory. Em outras palavras, quais parâmetros em seu ambiente local são exclusivos para seus colaboradores externos? Determine um parâmetro que distingua esses colaboradores externos dos membros de sua organização.

Duas abordagens comuns para isso são:

- Designe um atributo do Active Directory local não utilizado (por exemplo, extensionAttribute1) a ser usado como o atributo de origem. 
- Como alternativa, derive o valor do atributo UserType de outras propriedades. Por exemplo, você deseja sincronizar todos os usuários como Convidado se o atributo local UserPrincipalName do Active Directory terminar com o domínio *@partners.fabrikam123.org*.
 
Para mais detalhes sobre os requisitos de atributo, consulte [Habilitar a sincronização de UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurar o Azure AD Connect para sincronizar usuários com a nuvem

Depois de identificar o atributo exclusivo, poderá configurar o Azure AD Connect para sincronizar esses usuários com a nuvem como usuários do B2B do Azure Active Directory (ou seja, os usuários com UserType = Convidado). De um ponto de vista de autorização, não há diferença entre esses usuários e os usuários B2B criados pelo processo de convite de colaboração do B2B do Azure Active Directory.

Para obter instruções de implementação, consulte [Habilitar a sincronização de UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da colaboração B2B do Azure Active Directory, consulte [O que é a colaboração B2B do Azure Active Directory?](active-directory-b2b-what-is-azure-ad-b2b.md)
- Para uma visão geral do Azure AD Connect, consulte [Integrar seus diretórios locais ao Azure Active Directory](connect/active-directory-aadconnect.md).

