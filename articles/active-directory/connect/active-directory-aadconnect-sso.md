---
title: "Azure AD Connect: Logon Único Contínuo | Microsoft Docs"
description: "Este tópico descreve o Logon Único Contínuo do Azure AD (Azure Active Directory) e como ele permite que você forneça o verdadeiro logon único para usuários de área de trabalho corporativos dentro da sua rede corporativa."
services: active-directory
keywords: "o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 72b5217bd8de29fdad753d89f34934f64c551ff2
ms.contentlocale: pt-br
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Logon Único Contínuo do Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>O que é o Logon Único Contínuo do Azure Active Directory?

O SSO Contínuo do Azure AD (Logon Único Contínuo do Azure Active Directory) conecta usuários automaticamente quando estiverem nos respectivos dispositivos corporativos conectados à sua rede corporativa. Quando habilitado, os usuários não precisam digitar as senhas para entrar no Azure AD e, geralmente, nem precisam digitar os nomes de usuário. Esse recurso fornece aos usuários acesso fácil a seus aplicativos baseados em nuvem sem a necessidade de nenhum componente local adicional.

O SSO Contínuo pode ser combinado com o método de entrada de [Sincronização de Hash de Senha](active-directory-aadconnectsync-implement-password-synchronization.md) ou de [Autenticação de Passagem](active-directory-aadconnect-pass-through-authentication.md).

![Logon Único Contínuo](./media/active-directory-aadconnect-sso/sso1.png)

>[!NOTE]
>Esse recurso _não_ é aplicável aos Serviços de Federação do Active Directory (ADFS), que já incluem essa capacidade.

## <a name="key-benefits-of-using-azure-ad-seamless-sso"></a>Principais benefícios do uso do SSO Contínuo do Azure AD

- *Ótima experiência do usuário*
  - Os usuários são conectados automaticamente aos aplicativos baseados em nuvem e locais.
  - Os usuários não precisam digitar suas senhas repetidamente.
- *Fácil de implantar e administrar*
  - Não há necessidade de nenhum componente adicional local para fazer com que ele funcione.
  - Funciona com qualquer método de autenticação gerenciado – [Sincronização de Hash de Senha](active-directory-aadconnectsync-implement-password-synchronization.md) ou [Autenticação de Passagem](active-directory-aadconnect-pass-through-authentication.md).
  - Pode ser distribuído a alguns ou todos os seus usuários usando a Política de Grupo.
  - Registra dispositivos que não sejam do Windows 10 com o Azure AD. Isso precisa da versão 2.1 ou posterior do [cliente de ingresso no local de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Destaques do recurso

- O nome de usuário de conexão pode ser o nome de usuário local padrão (`userPrincipalName`) ou outro atributo configurado no Azure AD Connect (`Alternate ID`).
- O SSO Contínuo é um recurso oportunista. Se ele falhar por qualquer motivo, a experiência de entrada do usuário retornará ao comportamento normal, ou seja, o usuário precisará digitar sua senha na página de entrada.
- Se um aplicativo encaminhar um parâmetro `domain_hint` (identificando seu locatário) ou `login_hint` (identificando o usuário) na solicitação de entrada do Azure AD, os usuários serão automaticamente conectados sem inserirem nomes de usuário ou senhas.
- Isso pode ser habilitado por meio do Azure AD Connect.
- Essa é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.
- Há suporte para ele em clientes baseados em navegador da Web e clientes do Office que dão suporte à [autenticação moderna](https://aka.ms/modernauthga) em plataformas e navegadores que sejam compatíveis com a autenticação Kerberos:

| Sistema operacional\Navegador |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Sim|Sem suporte|Sim|Sim\*|N/D
|Windows 8.1|Sim|Sem suporte|Sim|Sim\*|N/D
|Windows 8|Sim|Sem suporte|Sim|Sim\*|N/D
|Windows 7|Sim|Sem suporte|Sim|Sim\*|N/D
|Mac OS X|N/D|N/D|Sim\*|Sim\*|Sem suporte

\*Exige [configuração adicional](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Para o Windows 10, a recomendação é usar o [Ingresso do Azure AD](../active-directory-azureadjoin-overview.md) para obter a experiência ideal de logon único com o Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [**Início Rápido** ](active-directory-aadconnect-sso-quick-start.md) – colocar o SSO Contínuo do Azure AD em funcionamento.
- [**Aprofundamento técnico**](active-directory-aadconnect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas frequentes**](active-directory-aadconnect-sso-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.

