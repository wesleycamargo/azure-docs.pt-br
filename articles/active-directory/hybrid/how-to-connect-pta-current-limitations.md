---
title: 'Azure AD Connect: Autenticação de Passagem – Limitações atuais | Microsoft Docs'
description: Este artigo descreve as limitações atuais da autenticação de passagem do Azure AD (Azure Active Directory)
services: active-directory
keywords: Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: d0816aebc033a0ac1c5b8a8f52106a586d20a9af
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54478273"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Autenticação de passagem do Azure Active Directory: Limitações atuais

>[!IMPORTANT]
>A Autenticação de Passagem do Azure AD (Azure Active Directory) é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo. A autenticação de Passagem só está disponível na instância mundial do Azure AD, e não na [nuvem do Microsoft Azure Alemanha](https://www.microsoft.de/cloud-deutschland) nem na [nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Cenários com suporte

Há suporte para os cenários a seguir:

- Entradas de usuário em aplicativos com base em navegador da Web.
- Entradas do usuário para clientes do Outlook usando protocolos herdados, como Exchange ActiveSync, EAS, SMTP, POP e IMAP.
- Credenciais de usuário para aplicativos cliente herdados do Office e aplicativos do Office compatíveis com [autenticação moderna](https://aka.ms/modernauthga): versões do Office 2010, 2013 e 2016.
- Entradas de usuário para aplicativos de protocolo herdados, como o PowerShell versão 1.0 e outros.
- Ingressos do Azure AD para dispositivos Windows 10.
- Senhas de aplicativo para Autenticação Multifator.

## <a name="unsupported-scenarios"></a>Cenários sem suporte

Os cenários a seguir _não_ têm suporte:

- Detecção de usuários com [credenciais vazadas](../reports-monitoring/concept-risk-events.md#leaked-credentials).
- Azure AD Domain Services precisa de sincronização de Hash de senha para ser habilitado no locatário. Portanto, os locatários que _somente_ usam a autenticação de passagem não funcionam para cenários que precisam de Azure AD Domain Services.
- A Autenticação de passagem não está integrada com [Azure AD Connect Health](whatis-hybrid-identity-health.md).

>[!IMPORTANT]
>Como uma solução alternativa para cenários sem suporte _apenas_ (exceto integração do Azure AD Connect Health), habilite a Sincronização de Hash de Senha na página [Recursos Opcionais](how-to-connect-install-custom.md#optional-features) no assistente do Azure AD Connect.

>[!NOTE]
Habilitar a Sincronização de Hash de Senha dará a opção de fazer failover da autenticação se a sua infraestrutura local sofrer uma interrupção. Esse failover da Autenticação de Passagem para a Sincronização de Hash de Senha não é automático. Você precisará alterar manualmente o método de entrada usando o Azure AD Connect. Se o servidor que está executando o Azure AD Connect ficar inativo, será preciso a ajuda do Suporte da Microsoft para desativar a Autenticação de passagem.

## <a name="next-steps"></a>Próximas etapas
- [Início rápido](how-to-connect-pta-quick-start.md): Instale e execute a Autenticação de Passagem do Azure Active Directory.
- [Migrar do AD FS para Autenticação de Passagem](https://aka.ms/ADFSTOPTADPDownload) – um guia detalhado para migrar do AD FS (ou outras tecnologias de federação) para Autenticação de Passagem.
- [Bloqueio Inteligente](../authentication/howto-password-smart-lockout.md): saiba como configurar o recurso Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [Análise técnica aprofundada](how-to-connect-pta-how-it-works.md): entenda como funciona o recurso Autenticação de passagem.
- [Perguntas frequentes](how-to-connect-pta-faq.md): Obtenha respostas para perguntas frequentes sobre o recurso Autenticação de Passagem.
- [Solucionar problemas](tshoot-connect-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso Autenticação de passagem.
- [Análise aprofundada sobre segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas aprofundadas sobre o recurso Autenticação de passagem.
- [SSO contínuo do Azure AD](how-to-connect-sso.md): saiba mais sobre este recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use o Fórum do Azure Active Directory para gerar novas solicitações de recursos.
