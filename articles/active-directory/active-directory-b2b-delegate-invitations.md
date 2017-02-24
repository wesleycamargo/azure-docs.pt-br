---
title: "Delegar convites para a colaboração do Azure Active Directory B2B | Microsoft Docs"
description: "As propriedades do usuário de colaboração B2B do Azure Active Directory podem ser configuradas"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 6c6d757a770613498bedca0f8f3a965241d692eb


---

# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegar convites para a colaboração do Azure Active Directory B2B

Com a atualização da visualização pública da colaboração B2B do Azure Active Directory (Azure AD), você não precisa ser um administrador global para convidar usuários. Controle quem pode convidar por meio de políticas e delegue convites aos usuários nas funções permitidas para convidar. Uma nova maneira importante de delegar convites de usuário convidado é por meio da função Emissor do convite para convidado.

## <a name="guest-inviter-role"></a>Função Emissor do convite para convidado
Podemos atribuir o usuário à função Emissor do convite para convidado para enviar convites. Você não precisa ser membro do administrador global para enviar convites. Por padrão, usuários regulares também podem chamar a API de convite, a menos que os convites tenham sido desabilitados pelo administrador global para usuários regulares. Você pode fazer isso no Portal do Azure e também no PowerShell.

Veja um exemplo que mostra a adição de um usuário à função Emissor do convite para convidado por meio do PowerShell:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress >
```

## <a name="controlling-who-can-invite"></a>Controle quem pode convidar

![controle como convidar](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Com a colaboração B2B do Azure AD, permitimos à administração de locatário definir as seguintes políticas de convite:

1. Desativar convites
2. Somente administradores e usuários na função Emissor de convite para convidado podem convidar
3. Administradores, função Emissor de convite para convidado e Membros podem convidar
4. Todos os usuários, incluindo convidados, podem convidar

Por padrão, os locatários são definidos como #4 (todos os usuários, incluindo convidados, podem convidar usuários B2B).

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriedades de usuário de colaboração B2B](active-directory-b2b-user-properties.md)
* [Adicionar um usuário de colaboração B2B a uma função](active-directory-b2b-add-guest-to-role.md)
* [Grupos dinâmicos e colaboração B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboração B2B e exemplos do PowerShell](active-directory-b2b-code-samples.md)
* [Configurar aplicativos SaaS para colaboração B2B](active-directory-b2b-configure-saas-apps.md)
* [Tokens de usuário de colaboração B2B](active-directory-b2b-user-token.md)
* [Mapeamento de declarações do usuário de colaboração B2B](active-directory-b2b-claims-mapping.md)
* [Compartilhamento externo do Office 365](active-directory-b2b-o365-external-user.md)
* [Limitações atuais da colaboração B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


