---
title: "API e personalização da colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "A colaboração B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
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
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: 235b551a716b68c40cc4fe346cc5903d47e6ea74


---

# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>API e personalização da colaboração B2B do Azure Active Directory

Muitos clientes nos disseram que desejam personalizar o processo de convite de forma que funcione melhor para suas organizações. Com nossa API, você pode fazer exatamente isso. [https://graph.microsoft.io/en-us/docs/api-reference/beta/resources/invitation](https://graph.microsoft.io/en-us/docs/api-reference/beta/resources/invitation)

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

  Nesse caso, você obterá novamente uma URL de resgate da API, que pode ser inserida em um modelo de email, mensagem instantânea ou outro método de distribuição de sua escolha.

6. Por fim, se você for administrador, poderá convidar o usuário como membro.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelo de Autorização
A API pode ser executada nos seguintes modos de autorização:

### <a name="app--user-mode"></a>Aplicativo + Modo de Usuário
Nesse modo, o usuário que está usando a API precisa ter as permissões para criar convites de B2B.

### <a name="app-only-mode"></a>Modo somente para aplicativo
No contexto exclusivo ao aplicativo, o aplicativo precisa dos escopos User.ReadWrite.All ou Directory.ReadWrite.All para que o convite tenha êxito.
Para saber mais, confira: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Agora é possível usar o PowerShell para adicionar e convidar facilmente usuários externos à organização. Basta criar um novo convite usando o cmdlet

```
New-AzureADMSInvitation
```

com as seguintes opções

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

cujas descrições seguem a referência da API de convite em [https://graph.microsoft.io/en-us/docs/api-reference/beta/resources/invitation](https://graph.microsoft.io/en-us/docs/api-reference/beta/resources/invitation)

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)
* [Autenticação multifator para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


