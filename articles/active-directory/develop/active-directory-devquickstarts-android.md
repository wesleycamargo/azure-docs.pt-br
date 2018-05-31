---
title: Introdução ao Android no Azure AD | Microsoft Docs
description: Como compilar um aplicativo para Android que se integre ao Azure AD para entrada e que chame as APIs protegidas do Azure AD usando o OAuth 2.0.
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 20618fff8d253bfab195ce2847a8848a28960ae4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155687"
---
# <a name="azure-ad-android-getting-started"></a>Introdução ao Android no Azure AD
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Caso esteja desenvolvendo um aplicativo do Android, a Microsoft torna simples e direta a entrada de usuários do Azure Active Directory (Azure AD). O Azure AD permite que seu aplicativo acesse dados de usuário por meio do Microsoft Graph ou sua própria API da Web protegida. 

A biblioteca do Android para ADAL (Biblioteca de Autenticação do Azure AD) dá a seu aplicativo a capacidade de começar a usar a [Nuvem do Microsoft Azure](https://cloud.microsoft.com) & [API do Microsoft Graph](https://graph.microsoft.io) dando suporte a [Contas do Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/) usando o padrão da indústria OAuth2 e o OpenID Connect. Esse exemplo demonstra todos os ciclos de vida normais que seu aplicativo deve apresentar, incluindo:

* Obter um token para o Microsoft Graph
* Atualizar um token
* Chamar o Microsoft Graph
* Sair do usuário

Para começar, você precisará de um locatário do Azure AD no qual possa criar usuários e registrar um aplicativo. Se você ainda não tiver um locatário [saiba como obter um](active-directory-howto-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Cenário: Conectar usuários e chamar o Microsoft Graph

![Topologia](./media/active-directory-android-topology.png)

Esse aplicativo pode ser usado para todas as contas do Azure AD. Ele dá suporte a cenários únicos e multiorganizacionais (discutidos em etapas). Ele demonstra como um desenvolvedor pode criar aplicativos para se conectar com os usuários corporativos e acessar seus dados do Azure + O365 por meio do Microsoft Graph. Durante o fluxo de autenticação, os usuários finais precisarão entrar e dar o consentimento para as permissões do aplicativo e, em alguns casos, pode ser preciso que um administrador dê consentimento ao aplicativo. A maioria da lógica neste exemplo mostra como autenticar um usuário final e fazer uma chamada básica para o Microsoft Graph.

## <a name="example-code"></a>Código de exemplo

Você pode encontrar o código de exemplo completo [no GitHub](https://github.com/Azure-Samples/active-directory-android). 

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="steps-to-run"></a>Etapas para executar

### <a name="register-and-configure-your-app"></a>Registrar e configurar seu aplicativo 
Você precisará ter um aplicativo cliente nativo registrado na Microsoft usando o [portal do Azure](https://portal.azure.com). 

1. Obtendo o registro do aplicativo
    - Navegue até o [Portal do Azure](https://aad.portal.azure.com). 
    - Selecione ***Azure Active Directory*** > ***Registros de Aplicativo***. 

2. Crie o aplicativo
    - Selecione **Novo registro de aplicativo**. 
    - Insira um nome de aplicativo no campo **Nome**. 
    - Em **Tipo de aplicativo**, selecione **Nativo**. 
    - NO **URI de redirecionamento**, insira `http://localhost`. 

3. Configurar o Microsoft Graph
    - Selecione **Configurações > Permissões Necessárias**.
    - Selecione **Adicionar**, e dentro de **Selecionar uma API**, selecione ***Microsoft Graph***. 
    - Selecione a permissão **Entrar e ler o perfil de usuário**, depois selecione **Selecionar** para salvar. 
        - Essa permissão mapeia para o escopo `User.Read`. 
    - Opcional: em **Permissões necessárias > Windows Azure Active Directory**, remova a permissão selecionada **Entrar e ler o perfil de usuário**. Isso evitará que a página de consentimento do usuário liste a permissão duas vezes. 

4. Parabéns! Seu aplicativo foi configurado com êxito. Na próxima seção, você precisará:
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>Obter o código de amostra

1. Clonar o código.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Abrir o exemplo no Android Studio.
    - Selecionar **Abrir um projeto existente do Android Studio**.

### <a name="configure-your-code"></a>Configurar seu código

Você pode encontrar todas as configurações deste exemplo de código no arquivo ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java***. 

1. Substitua a constante `CLIENT_ID` por `ApplicationID`.
2. Substitua a constante `REDIRECT URI` pelo `Redirect URI` configurado anteriormente (`http://localhost`). 

### <a name="run-the-sample"></a>Execute o exemplo

1. Selecione **Compilar > Limpar Projeto**. 
2. Selecione **Executar > Executar aplicativo**. 
3. O aplicativo deve criar e mostrar alguns UX básicos. Quando você clica no botão `Call Graph API`, ele solicitará a entrada e chamará silenciosamente a API do Microsoft Graph com o novo token. 

## <a name="important-info"></a>Informações importantes

1. Confira a [Wiki do Android para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki) para obter mais informações sobre a mecânica da biblioteca e como configurar novos cenários e recursos. 
2. Em cenários nativos, o aplicativo usará um Webview integrado e não sairá do aplicativo. O `Redirect URI` pode ser arbitrário. 
3. Encontrou problemas ou tem solicitações? Você pode abrir um tíquete ou postar no Stackoverflow com a marca `azure-active-directory`. 

### <a name="cross-app-sso"></a>SSO entre aplicativos
Saiba [como habilitar o SSO entre aplicativos no Android usando a ADAL](active-directory-sso-android.md). 

### <a name="auth-telemetry"></a>Telemetria de autenticação
A biblioteca ADAL expõe a telemetria de autenticação para ajudar os desenvolvedores de aplicativo a entenderem como seus aplicativos estão se comportando para criar melhores experiências. Isso permite que você capture entradas com êxito, usuários ativos e vários outros insights interessantes. O uso da telemetria de autenticação requer que os desenvolvedores de aplicativo estabeleçam um serviço de telemetria para agregar e armazenar eventos.

Para saber mais sobre a telemetria de autenticação, confira [telemetria de autenticação do Android para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry). 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
