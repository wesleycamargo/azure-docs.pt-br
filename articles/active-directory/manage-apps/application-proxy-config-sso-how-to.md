---
title: Como configurar o logon único para um aplicativo de Application Proxy | Microsoft Docs
description: Como você pode configurar o logon único para um aplicativo de Application Proxy rapidamente
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: celested
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f9c4f2c618851b596cd8f2dfa1fd45820a05049
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442635"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Como configurar o logon único para um aplicativo de Application Proxy

Logon único (SSO) permite que os usuários acessem um aplicativo sem autenticação, várias vezes. Ele permite que a autenticação única que ocorre na nuvem, no Azure Active Directory, e permite que o serviço ou o conector represente o usuário para concluir os desafios de autenticação adicional do aplicativo.

## <a name="how-to-configure-single-sign-on"></a>Como configurar o logon único
Para configurar SSO, primeiro certifique-se de que seu aplicativo está configurado para pré-autenticação através do Azure Active Directory. Para fazer essa configuração, vá para **Azure Active Directory** -&gt;**Aplicativos empresariais** -&gt;**Todos os aplicativos** -&gt; Seu aplicativo **-&gt; Application Proxy**. Nessa página, consulte o campo "Pré-autenticação" e certifique-se que é definido como "Azure Active Directory". 

Para obter mais informações sobre os métodos de pré-autenticação, consulte a etapa 4 do [documento de publicação de aplicativo](application-proxy-add-on-premises-application.md).

   ![O método de pré-autenticação no portal do Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configurando modos de logon único para aplicativos do Application Proxy
Configure o tipo específico de logon único. Os métodos de logon são classificados com base no tipo de autenticação do aplicativo que o back-end usa. Aplicativos de Application Proxy oferecem suporte a três tipos de logon:

-   **Logon baseado em senha**: O logon baseado em senha pode ser usado para qualquer aplicativo que usa campos de nome de usuário e senha para logon. As etapas de configuração estão em [Configurar o logon único com senha para um aplicativo da galeria do Azure AD](configure-password-single-sign-on-gallery-applications.md).

-   **Autenticação Integrada do Windows**: Para aplicativos que usam IWA (Autenticação Integrada do Windows), o logon único é habilitado por meio de KCD (Delegação Restrita de Kerberos). Esse método concede permissão de conectores do Application Proxy no Active Directory para representar os usuários e para enviar e receber tokens em seu nome. Detalhes sobre como configurar o KCD podem ser encontrados no [Logon único com documentação de KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Logon baseado em cabeçalho**: O logon baseado em cabeçalho é habilitado por meio de uma parceria e exige alguma configuração adicional. Para obter detalhes sobre a parceria e instruções passo a passo para configurar o logon único para um aplicativo que usa cabeçalhos para autenticação, consulte o [PingAccess para a documentação do Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **Logon único SAML**: Com o SAML SSO, Azure AD autentica o aplicativo usando a conta do usuário do Azure AD. O Azure AD comunica as informações do logon para o aplicativo por meio de um protocolo de conexão. Com o logon único baseado em SAML, você pode mapear os usuários para funções de aplicativo específicas com base nas regras definidas nas suas declarações SAML. Para obter informações sobre como configurar o logon único SAML, consulte [SAML para logon único com Proxy de aplicativo](application-proxy-configure-single-sign-on-on-premises-apps.md).

Cada uma dessas opções pode ser encontrada indo em seu aplicativo em "Aplicativos empresariais" e abrindo a página **Logon Único** no menu à esquerda. Observe que, se seu aplicativo foi criado no portal antigo, você talvez não veja todas essas opções.

Nessa página, você também visualiza uma opção de logon adicional: Logon vinculado. Essa opção também tem suporte do Application Proxy. No entanto, essa opção não adiciona o logon único para o aplicativo. Isso posto, o aplicativo pode já ter logon único implementado usando outro serviço, como Serviços de Federação do Active Directory. 

Essa opção permite que um administrador crie um link para um aplicativo que os usuários encontram ao acessar o aplicativo. Por exemplo, se houver um aplicativo que está configurado para autenticar usuários usando os Serviços de Federação do Active Directory 2.0, um administrador poderá usar a opção "Logon vinculado" para criar um link para ele no painel de acesso.

## <a name="next-steps"></a>Próximas etapas
- [Compartimentação de senhas para logon único com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Delegação restrita de Kerberos para logon único com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
- [Autenticação baseada em cabeçalho para logon único com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML para logon único com Proxy de aplicativo](application-proxy-configure-single-sign-on-on-premises-apps.md).
