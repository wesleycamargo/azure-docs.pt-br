---
title: "Azure AD Connect: Autenticação de Passagem – como ela funciona | Microsoft Docs"
description: "Este artigo descreve como a Autenticação de Passagem do Azure Active Directory funciona"
services: active-directory
keywords: "Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 00420e07957e7d8fe68bb8f1a1742110429d16b8
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticação de Passagem do Azure Active Directory: aprofundamento técnico
Este artigo descreve como funciona a Autenticação de Passagem do Azure AD (Azure Active Directory). Para obter informações técnicas e de segurança aprofundadas, veja o artigo [Aprofundamento sobre segurança](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md).

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Como a Autenticação de Passagem do Azure Active Directory funciona?

Quando um usuário tenta entrar em um aplicativo protegido pelo Azure AD, e se a Autenticação de Passagem está habilitada no locatário, ocorrem as seguintes etapas:

1. O usuário tenta acessar um aplicativo, por exemplo, [Outlook Web App](https://outlook.office365.com/owa/).
2. Se o usuário ainda não tiver entrado, ele será redirecionado para a página **Entrada de Usuário** do Azure AD.
3. O usuário insere o nome de usuário e a senha na página de entrada do Azure AD e seleciona o botão **Entrar**.
4. O Azure AD, ao receber a solicitação de entrada, coloca o nome de usuário e a senha (criptografada com o uso de uma chave pública) em uma fila.
5. Um Agente de Autenticação local recupera o nome de usuário e a senha criptografada da fila.
6. O agente descriptografa a senha usando sua chave privada.
7. Em seguida, o agente valida o nome de usuário e a senha no Active Directory usando APIs padrão do Windows, que é um mecanismo semelhante ao usado pelos Serviços de Federação do Active Directory (AD FS). O nome de usuário pode ser o nome de usuário local padrão, normalmente `userPrincipalName`, ou outro atributo configurado no Azure AD Connect (também conhecido como `Alternate ID`).
8. Em seguida, o DC (Controlador de Domínio) do Active Directory local avalia a solicitação e retorna a resposta apropriada (êxito, falha, senha expirada ou usuário bloqueado) para o agente.
9. O Agente de Autenticação, por sua vez, retorna essa resposta ao Azure AD.
10. Azure AD avalia a resposta e responde ao usuário conforme apropriado. Por exemplo, o Azure AD autentica o usuário imediatamente ou solicita a Autenticação Multifator do Azure.
11. Se a entrada do usuário for bem-sucedida, ele será capaz de acessar o aplicativo.

O diagrama a seguir ilustra a todos os componentes e as etapas envolvidas:

![Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Próximas etapas
- [Limitações atuais](active-directory-aadconnect-pass-through-authentication-current-limitations.md): saiba quais cenários têm suporte e quais não têm.
- [Início rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md): instale e execute a Autenticação de Passagem do Azure AD.
- [Bloqueio Inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): configure a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [Perguntas frequentes](active-directory-aadconnect-pass-through-authentication-faq.md): encontre respostas para perguntas frequentes.
- [Solução de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de Autenticação de Passagem.
- [Aprofundamento em segurança](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): obtenha informações técnicas avançadas sobre o recurso de Autenticação de passagem.
- [SSO contínuo do Azure AD](active-directory-aadconnect-sso.md): saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use o Fórum do Azure Active Directory para arquivar novas solicitações.

