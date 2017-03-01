---
title: "Autenticação Multifator para usuários de colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "A colaboração B2B do Azure Active Directory dá suporte à autenticação multifator (MF) para acesso seletivo aos seus aplicativos corporativos"
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
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 32d0b45080d57712209e0c5a3e5adf981fb4b66e
ms.lasthandoff: 02/17/2017


---

# <a name="multi-factor-authentication-for-azure-active-directory-b2b-collaboration-users"></a>Autenticação Multifator para usuários de colaboração B2B do Azure Active Directory

Com essa atualização de visualização pública da colaboração B2B do Azure AD, estamos introduzindo a possibilidade de as organizações aplicarem as políticas de MFA (autenticação multifator) também aos usuários da colaboração B2B. Nessa atualização, a MFA sempre é aplicada no locatário do recurso.

Isso significa que:
1. O Administrador ou Operador de informações na Empresa A convida o usuário da Empresa B para um aplicativo Foo na Empresa A.
2. O aplicativo *Foo* na empresa A está configurado para exigir MFA no acesso.
3. Quando o usuário da empresa B tenta acessar o aplicativo Foo do locatário da empresa A, receberá uma solicitação para concluir um desafio de MFA, conforme exigido pela política da MFA da empresa.
4. Usuário pode configurar sua MFA com a empresa A, e escolher a opção de MFA
5. Isso funcionará para qualquer identidade (Azure AD ou MSA, por exemplo, se os usuários na Empresa B autenticarem usando a ID social)
6. A Empresa A precisará ter SKUs premium do Azure AD adequados que ofereçam suporte a MFA. O usuário da Empresa B consumirá essa licença da Empresa A.
7. Resumindo, o locatário que convida *sempre* é responsável pela MFA dos usuários de colaboração B2B da organização parceira, e não pela organização parceira (mesmo que tenha recursos de MFA). Em versões futuras, permitiremos que organização que convida confie na MFA específica das organizações parceiras, em vez de usar a MFA da organização que convida.

## <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Definindo a MFA para usuários de colaboração B2B
Para descobrir como é fácil configurar a MFA para usuários de colaboração B2B, confira o vídeo a seguir:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

## <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Experiência de MFA de usuários B2B para resgate de oferta
Confira a animação abaixo para ver a experiência de resgate, conforme mostrado no vídeo a seguir:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

## <a name="mfa-reset-for-b2b-collaboration-users"></a>Redefinição de MFA para usuários de colaboração B2B
Atualmente, o administrador pode exigir que os usuários de colaboração B2B façam uma verificação novamente usando somente os seguintes cmdlets do PowerShell. Portanto, os seguintes cmdlets do PowerShell devem ser usados se você quiser redefinir o método de verificação do usuário de colaboração B2B.

> [!NOTE]
> Para usar o novo cmdlet, instale o módulo do PowerShell V2 do Azure AD, que pode ser obtido aqui: https://www.powershellgallery.com/packages/AzureADPreview

1. Conecte-se ao AD do Azure

  ```
  Connect-MsolService and login
  ```
2. Obter todos os usuários com métodos de verificação

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Aqui está um exemplo:

  ```
  PS C:\Users\tjwasser> Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName,
  @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Redefina o método de MFA para um usuário específico. Você pode usar esse UserPrincipalName para executar o comando reset a fim de exigir que o usuário de colaboração B2B defina os métodos de verificação novamente. Exemplo:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

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
* [API e personalização da colaboração B2B do Azure Active Directory](active-directory-b2b-api.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

