---
title: Delegar convites para a colaboração do Azure Active Directory B2B | Microsoft Docs
description: As propriedades do usuário de colaboração B2B do Azure Active Directory podem ser configuradas
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: fd00fb8da3cf36518f9e28e827e59fd7ff45d687
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622203"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Delegar convites para a colaboração do Azure Active Directory B2B

Com a colaboração B2B (entre empresas) do Azure AD (Azure Active Directory), você não precisa ser um administrador global para enviar convites. Em vez disso, use políticas e delegue convites aos usuários cujas funções permitem o envio de convites. Uma nova maneira importante de delegar convites de usuário convidado é por meio da função Emissor do convite para convidado.

## <a name="guest-inviter-role"></a>Função Emissor do convite para convidado
Podemos atribuir o usuário à função Emissor do convite para convidado para enviar convites. Você não precisa ser membro da função de administrador global para enviar convites. Por padrão, usuários regulares também podem chamar a API de convite, a menos que os convites tenham sido desabilitados pelo administrador global para usuários regulares. Um usuário também pode chamar a API usando o Portal do Azure ou o PowerShell.

Veja um exemplo que mostra como usar o PowerShell para adicionar um usuário à função Emissor de convite para convidado:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Controle quem pode convidar

Acesse o Azure Active Directory > Configurações de Usuário > Gerenciar configurações de colaboração externa

![externalusers](https://user-images.githubusercontent.com/13383753/45905128-2c47f680-bda4-11e8-955d-6219c67935e0.PNG)

Com a colaboração B2B do Azure AD, um administrador de locatário pode definir as seguintes políticas de convite:

- Desativar convites
- Somente administradores e usuários na função Emissor de convite para convidado podem convidar
- Administradores, a função Emissor de convite para convidado e membros podem convidar
- Todos os usuários, incluindo convidados, podem convidar

Por padrão, os locatários são definidos como #4. (Todos os usuários, incluindo convidados, podem convidar usuários B2B).

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos na colaboração B2B do Azure AD:

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Adicionar usuários convidados da colaboração B2B sem um convite](add-user-without-invite.md)
- [Adicionar um usuário de colaboração B2B a uma função](add-guest-to-role.md)


