---
title: "Azure AD Connect: autenticação de passagem – Limitações atuais | Microsoft Docs"
description: "Este artigo descreve as limitações atuais da autenticação de passagem do Azure AD (Azure Active Directory)."
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
ms.date: 08/03/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 37c0ea094d02208f2516a4a040f75894e046c670
ms.contentlocale: pt-br
ms.lasthandoff: 08/04/2017

---

# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticação de passagem do Azure Active Directory: limitações atuais

>[!IMPORTANT]
>A autenticação de passagem do Azure AD está atualmente na versão prévia. Essa é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo. A autenticação de passagem só está disponível na instância mundial do Azure AD e não no [Microsoft Cloud Alemanha](http://www.microsoft.de/cloud-deutschland) nem na [Nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Cenários com suporte

Os cenários a seguir têm suporte total durante a visualização:

- Entradas de usuário em todos os aplicativos baseados em navegador da Web.
- Entradas de usuário em aplicativos cliente Office 365 com suporte para [autenticação moderna](https://aka.ms/modernauthga).
- Ingresso do Azure AD para dispositivos Windows 10.
- Suporte ao Exchange ActiveSync.

## <a name="unsupported-scenarios"></a>Cenários sem suporte

Os cenários a seguir _não_ têm suporte durante a versão prévia:

- Entradas do usuário em aplicativos de cliente herdados do Office (Office 2013 ou anterior). As organizações são incentivadas a mudar para autenticação moderna se possível. A autenticação moderna permite o suporte à Autenticação de Passagem, mas também ajuda a proteger suas contas de usuário usando recursos de [acesso condicional](../active-directory-conditional-access.md) como a MFA (Autenticação Multifator).
- O usuário entra em aplicativos cliente do Skype for Business, incluindo o Skype for Business 2016.
- Logons de usuário no PowerShell v1.0. É recomendável usar o PowerShell v2.0.

>[!IMPORTANT]
>Como alternativa para cenários sem suporte, habilite a sincronização de hash de senha na página [Recursos opcionais](active-directory-aadconnect-get-started-custom.md#optional-features) do assistente do Azure AD Connect. A sincronização de hash de senha age como um fallback _somente_ para os cenários anteriores (e _não_ como um fallback genérico para autenticação de passagem). Se você não precisar desses cenários, desligue a sincronização de hash de senha.

## <a name="next-steps"></a>Próximas etapas
- [**Início rápido**](active-directory-aadconnect-pass-through-authentication-quick-start.md) – instale e execute a autenticação de passagem do Azure AD.
- [**Aprofundamento técnico**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas frequentes**](active-directory-aadconnect-pass-through-authentication-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Saiba como resolver problemas comuns do recurso.
- [**SSO contínuo do Azure AD**](active-directory-aadconnect-sso.md) – Saiba mais sobre esse recurso complementar.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.

