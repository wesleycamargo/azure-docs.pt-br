---
title: API e personalização da colaboração B2B do Azure Active Directory | Microsoft Docs
description: A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/11/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f1bd93ac2ef6aa75e07eeec3e3cb2222b6febc1c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932363"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>API e personalização da colaboração B2B do Azure Active Directory

Muitos clientes nos disseram que desejam personalizar o processo de convite de forma que funcione melhor para suas organizações. Com nossa API, você pode fazer exatamente isso. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Recursos da API de convite
A API oferece os seguintes recursos:

1. Convidar um usuário com *qualquer* endereço de email.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Personalizar onde você deseja que os usuários cheguem depois de aceitar o convite.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Optar por enviar o email de convite padrão por nosso intermédio

    ```
    "sendInvitationMessage": true
    ```

  com uma mensagem para o destinatário que você pode personalizar

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. E escolher copiar (cc:) as pessoas que você deseja manter no loop sobre o seu convite a esse colaborador.

5. Ou personalizar completamente seu convite e o fluxo de integração ao optar por não enviar notificações por meio do Azure AD.

    ```
    "sendInvitationMessage": false
    ```

  Nesse caso, você obtém novamente uma URL de resgate da API, que pode ser inserida em um modelo de email, mensagem instantânea ou outro método de distribuição de sua escolha.

6. Por fim, se você for administrador, poderá convidar o usuário como membro.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelo de autorização
A API pode ser executada nos seguintes modos de autorização:

### <a name="app--user-mode"></a>Modo Aplicativo + Usuário
Nesse modo, a pessoa que está usando a API precisa ter as permissões para criar convites de B2B.

### <a name="app-only-mode"></a>Modo Somente aplicativo
No contexto somente aplicativo, o aplicativo precisa do escopo User.Invite.All para que o convite tenha êxito.

Para obter mais informações, consulte: https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Agora é possível usar o PowerShell para adicionar e convidar facilmente usuários externos à organização. Crie um convite usando o cmdlet:

```
New-AzureADMSInvitation
```

É possível usar as seguintes opções:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Você também pode consultar a referência de API de convite no [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
- [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
- [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)

