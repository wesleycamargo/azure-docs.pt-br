---
title: 'Azure AD Connect: autenticação de passagem – Limitações atuais | Microsoft Docs'
description: Este artigo descreve as limitações atuais da autenticação de passagem do Azure AD (Azure Active Directory)
services: active-directory
keywords: Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: 680e9967010771b8e3651c6f4eed81237f8fb4c3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticação de passagem do Azure Active Directory: limitações atuais

>[!IMPORTANT]
>A Autenticação de Passagem do Azure AD (Azure Active Directory) é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo. A autenticação de Passagem só está disponível na instância mundial do Azure AD, e não na [nuvem do Microsoft Azure Alemanha](http://www.microsoft.de/cloud-deutschland) nem na [nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Cenários com suporte

Os cenários a seguir têm suporte total:

- Entradas de usuário em todos os aplicativos com base em navegador da Web.
- Entradas de usuário em aplicativos Office com suporte para [autenticação moderna](https://aka.ms/modernauthga): Office 2016 e Office 2013 _com_ autenticação moderna.
- Entradas do usuário para clientes do Outlook usando protocolos herdados, como Exchange ActiveSync, SMTP, POP e IMAP.
- Entradas do usuário para o Skype for Business que suportam autenticação moderna, incluindo topologias online e híbridas. Saiba mais sobre topologias com suporte [aqui](https://technet.microsoft.com/library/mt803262.aspx).
- Ingressos no domínio do Azure AD para dispositivos do Windows 10.
- Senhas de aplicativo para Autenticação Multifator.

## <a name="unsupported-scenarios"></a>Cenários sem suporte

Os cenários a seguir _não_ têm suporte:

- Entradas do usuário para aplicativos de cliente do Office herdados, com exceção do Outlook (consulte **Cenários com suporte** acima): Office 2010 e Office 2013 _sem_ autenticação moderna. As organizações são incentivadas a mudar para autenticação moderna se possível. A autenticação moderna possibilita o suporte à Autenticação de Passagem. Também ajuda a proteger suas contas de usuário usando recursos de [acesso condicional](../active-directory-conditional-access-azure-portal.md), como Autenticação Multifator do Azure.
- Acesso ao compartilhamento de calendário e informações livres/ocupadas nos ambientes híbridos do Exchange somente no Office 2010.
- O usuário entra em aplicativos cliente do Skype for Business, _sem_ autenticação moderna.
- Entradas de usuário no PowerShell versão 1.0. Recomendamos usar o PowerShell versão 2.0.
- Detecção de usuários com [credenciais vazadas](../active-directory-reporting-risk-events.md#leaked-credentials).
- Azure AD Domain Services precisa de sincronização de Hash de senha para ser habilitado no locatário. Portanto, os locatários que _somente_ usam a autenticação de passagem não funcionam para cenários que precisam de Azure AD Domain Services.
- A Autenticação de passagem não está integrada com [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).
- O Programa de Registro de Dispositivos da Apple (DEP da Apple) usando o Assistente de Configuração do iOS não é compatível com a autenticação moderna. Isso fará com que dispositivos do DEP da Apple não consigam ser registrados no Intune para domínios gerenciados usando a Autenticação de Passagem. Considere o uso do [aplicativo Portal da Empresa](https://blogs.technet.microsoft.com/intunesupport/2018/02/08/support-for-multi-token-dep-and-authentication-with-company-portal/) como uma alternativa.

>[!IMPORTANT]
>Como alternativa para cenários sem suporte _apenas_, habilite a sincronização de hash de senha na página [Recursos opcionais](active-directory-aadconnect-get-started-custom.md#optional-features) do assistente do Azure AD Connect. Quando os usuários entram nos aplicativos listados na seção "cenários sem suporte", essas solicitações de entrada específicas _não_ são tratadas por Agentes de autenticação de passagem e, portanto, não serão registradas no [Logs de autenticação de passagem](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs).

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
