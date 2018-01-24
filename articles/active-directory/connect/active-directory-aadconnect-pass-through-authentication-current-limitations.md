---
title: "Azure AD Connect: autenticação de passagem – Limitações atuais | Microsoft Docs"
description: "Este artigo descreve as limitações atuais da autenticação de passagem do Azure AD (Azure Active Directory)"
services: active-directory
keywords: "Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: billmath
ms.openlocfilehash: aee90f278476a899e0d47fc572c4f375bf926de2
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticação de passagem do Azure Active Directory: limitações atuais

>[!IMPORTANT]
>A Autenticação de Passagem do Azure AD (Azure Active Directory) é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo. A autenticação de Passagem só está disponível na instância mundial do Azure AD, e não na [nuvem do Microsoft Azure Alemanha](http://www.microsoft.de/cloud-deutschland) nem na [nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Cenários com suporte

Os cenários a seguir têm suporte total:

- Entradas de usuário em todos os aplicativos baseados em navegador da Web
- Entradas de usuário em aplicativos Office com suporte para [autenticação moderna](https://aka.ms/modernauthga): Office 2016 e Office 2013 _com_ autenticação moderna
- Entradas do usuário para o Skype for Business que suportam autenticação moderna, incluindo topologias online e híbrida. Saiba mais sobre topologias com suporte [aqui](https://technet.microsoft.com/library/mt803262.aspx).
- Ingressos em domínio do Azure AD para dispositivos com Windows 10
- Suporte ao Exchange ActiveSync

## <a name="unsupported-scenarios"></a>Cenários sem suporte

Os cenários a seguir _não_ têm suporte:

- Entradas do usuário em aplicativos de cliente do Office herdados – Office 2010 e Office 2013 _sem_ autenticação moderna. As organizações são incentivadas a mudar para autenticação moderna se possível. A autenticação moderna possibilita o suporte à Autenticação de Passagem. Também ajuda a proteger suas contas de usuário usando recursos de [acesso condicional](../active-directory-conditional-access-azure-portal.md), como Autenticação Multifator do Azure.
- O usuário entra em aplicativos cliente do Skype for Business, _sem_ autenticação moderna.
- Entradas de usuário no PowerShell versão 1.0. Recomendamos usar o PowerShell versão 2.0.
- Senhas de aplicativo para Autenticação Multifator.
- Detecção de usuários com [credenciais vazadas](../active-directory-reporting-risk-events.md#leaked-credentials).
- Azure AD Domain Services precisa de sincronização de Hash de senha para ser habilitado no locatário. Portanto, os locatários que _somente_ usam a autenticação de passagem não funcionam para cenários que precisam de Azure AD Domain Services.
- A Autenticação de passagem não está integrada com [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- O Programa de registro de dispositivos (DEP da Apple) Apple não é compatível com a autenticação moderna.  Dispositivos de DEP da Apple falharão ao ser registrados no Intune para domínios usando a Autenticação de Passagem.

>[!IMPORTANT]
>Como alternativa para cenários sem suporte _apenas_, habilite a sincronização de hash de senha na página [Recursos opcionais](active-directory-aadconnect-get-started-custom.md#optional-features) do assistente do Azure AD Connect.

>[!NOTE]
Habilitar a sincronização de hash de senha oferece a opção de fazer failover da autenticação se sua infraestrutura local sofrer uma interrupção. Esse failover da Autenticação de Passagem para a sincronização de hash de senha do Active Directory não é automático. Você precisará alterar manualmente o método de entrada usando o Azure AD Connect. Se o servidor que está executando o Azure AD Connect ficar inativo, será preciso a ajuda do Suporte da Microsoft para desativar a Autenticação de passagem.

## <a name="next-steps"></a>Próximas etapas
- [Início rápido](active-directory-aadconnect-pass-through-authentication-quick-start.md): instale e execute com a Autenticação de Passagem do Azure AD.
- [Bloqueio Inteligente](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): saiba como configurar a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [Análise técnica aprofundada](active-directory-aadconnect-pass-through-authentication-how-it-works.md): entenda como funciona o recurso de Autenticação de passagem.
- [Perguntas frequentes](active-directory-aadconnect-pass-through-authentication-faq.md): obtenha respostas para perguntas frequentes sobre o recurso de Autenticação de Passagem.
- [Solução de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de Autenticação de Passagem.
- [Aprofundamento em segurança](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): obtenha informações técnicas avançadas sobre o recurso de Autenticação de passagem.
- [SSO contínuo do Azure AD](active-directory-aadconnect-sso.md): saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use o Fórum do Azure Active Directory para arquivar novas solicitações.

