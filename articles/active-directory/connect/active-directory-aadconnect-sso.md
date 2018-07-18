---
title: 'Azure AD Connect: Logon Único Contínuo | Microsoft Docs'
description: Este tópico descreve o Logon Único Contínuo do Azure AD (Azure Active Directory) e como ele permite que você forneça o verdadeiro logon único para usuários de área de trabalho corporativos dentro da sua rede corporativa.
services: active-directory
keywords: o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ad7c412ee92db53dd797e38df2fc6db0a762fe78
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916159"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Logon Único Contínuo do Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>O que é o Logon Único Contínuo do Azure Active Directory?

O SSO Contínuo do Azure AD (Logon Único Contínuo do Azure Active Directory) conecta usuários automaticamente quando estiverem nos respectivos dispositivos corporativos conectados à sua rede corporativa. Quando habilitado, os usuários não precisam digitar as senhas para entrar no Azure AD e, geralmente, nem precisam digitar os nomes de usuário. Esse recurso fornece aos usuários acesso fácil a seus aplicativos baseados em nuvem sem a necessidade de nenhum componente local adicional.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

O SSO Contínuo pode ser combinado com o método de entrada de [Sincronização de Hash de Senha](active-directory-aadconnectsync-implement-password-hash-synchronization.md) ou de [Autenticação de Passagem](active-directory-aadconnect-pass-through-authentication.md).

![Logon Único Contínuo](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>O SSO contínuo _não_ é aplicável aos Serviços de Federação do Active Directory (AD FS).

## <a name="key-benefits"></a>Principais benefícios

- *Ótima experiência do usuário*
  - Os usuários são conectados automaticamente aos aplicativos baseados em nuvem e locais.
  - Os usuários não precisam digitar suas senhas repetidamente.
- *Fácil de implantar e administrar*
  - Não há necessidade de nenhum componente adicional local para fazer com que ele funcione.
  - Funciona com qualquer método de autenticação de nuvem – [Sincronização de hash de senha](active-directory-aadconnectsync-implement-password-hash-synchronization.md) ou [Autenticação de passagem](active-directory-aadconnect-pass-through-authentication.md).
  - Pode ser distribuído a alguns ou todos os seus usuários usando a Política de Grupo.
  - Registre dispositivos não Windows 10 no Azure AD sem a necessidade de nenhuma infraestrutura do AD FS. Esse recurso deve usar a versão 2.1 ou posterior do [cliente workplace-join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Destaques do recurso

- O nome de usuário de conexão pode ser o nome de usuário local padrão (`userPrincipalName`) ou outro atributo configurado no Azure AD Connect (`Alternate ID`). Ambos casos de uso funcionam porque o SSO Contínuo usa a declaração `securityIdentifier` no tíquete do Kerberos para pesquisar o objeto de usuário correspondente no Azure AD.
- O SSO Contínuo é um recurso oportunista. Se ele falhar por qualquer motivo, a experiência de entrada do usuário retornará ao comportamento normal, ou seja, o usuário precisará digitar sua senha na página de entrada.
- Se um aplicativo (por exemplo, https://myapps.microsoft.com/contoso.com) encaminhar um parâmetro `domain_hint` (OpenID Connect) ou `whr` (SAML) - identificando seu locatário, ou um parâmetro `login_hint` - identificando o usuário, na solicitação de entrada do Microsoft Azure AD, os usuários serão automaticamente conectados sem inserir nomes de usuário ou senhas.
- Os usuários também terão uma experiência de logon silenciosa se um aplicativo (por exemplo, https://contoso.sharepoint.com)) enviar solicitações de entrada para pontos de extremidade com locatários do Microsoft Azure AD - ou seja, https://login.microsoftonline.com/contoso.com/<..> ou https://login.microsoftonline.com/<tenant_ID>/<..> - em vez do ponto de extremidade comum do Microsoft Azure AD - ou seja, https://login.microsoftonline.com/common/<...>.
- Há suporte para saída. Isso permite que os usuários escolham outra conta do Azure AD para conectar, em vez de conectar automaticamente usando o SSO contínuo automaticamente.
- Os clientes do Office 365 (16.0.8730.xxxx e acima) têm suporte usando um fluxo não interativo.
- Isso pode ser habilitado por meio do Azure AD Connect.
- Essa é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.
- Há suporte para ele em clientes baseados em navegador da Web e clientes do Office que dão suporte à [autenticação moderna](https://aka.ms/modernauthga) em plataformas e navegadores que sejam compatíveis com a autenticação Kerberos:

| Sistema operacional\Navegador |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|sim|Não |sim|Sim\*|N/D
|Windows 8.1|sim|N/D|sim|Sim\*|N/D
|Windows 8|sim|N/D|sim|Sim\*|N/D
|Windows 7|sim|N/D|sim|Sim\*|N/D
|Mac OS X|N/D|N/D|Sim\*|Sim\*|Sim\*

\*Exige [configuração adicional](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Para o Windows 10, a recomendação é usar o [Ingresso do Azure AD](../active-directory-azureadjoin-overview.md) para obter a experiência ideal de logon único com o Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [**Início Rápido** ](active-directory-aadconnect-sso-quick-start.md) – colocar o SSO Contínuo do Azure AD em funcionamento.
- [**Aprofundamento técnico**](active-directory-aadconnect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas frequentes**](active-directory-aadconnect-sso-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
