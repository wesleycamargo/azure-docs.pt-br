---
title: Início Rápido Android do Azure AD v2 | Microsoft Docs
description: Saiba como aplicativos Android podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35cf0f7ffe118a3c878e14e4a81793d18a646fa9
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202879"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início Rápido: Entrada de usuários e chamada à API do Microsoft Graph em um aplicativo Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Este início rápido contém um exemplo de código que demonstra como um aplicativo Android pode conectar contas corporativas, pessoais ou de estudante, obter um token de acesso e chamar a API do Microsoft Graph.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-android/android-intro-updated.png)

> [!NOTE]
> **Pré-requisitos**
> * Android Studio 3 ou posterior
> * O SDK 21 do Android ou mais recente é necessário (o recomendado é o SDK 27)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Registrar e baixar
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registre e configure seu aplicativo e exemplo de código
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, faça o seguinte:
> 1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo.
> 1. No campo **Nome do Aplicativo**, insira um nome para o seu aplicativo.
> 1. Desmarque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.
> 1. Selecione **Adicionar Plataforma**, selecione **Aplicativo Nativo** e, em seguida, selecione **Salvar**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Etapa 1: Configurar seu aplicativo
> Para o exemplo de código para este início rápido funcionar, você precisará adicionar uma URL de resposta como **msal{AppId}://auth** (em que {AppId} é a ID do aplicativo do seu aplicativo).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-android/green-check.png) Seu aplicativo já está configurado com esses atributos

#### <a name="step-2-download-the-project"></a>Etapa 2: Baixe o projeto

* [Baixar o projeto do Android Studio](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Etapa 3: Configurar seu projeto

1. Extraia e abra o projeto no Android Studio.
1. Em **aplicativo** > **java** > **<i>{host}.{namespace}</i>**, abra **MainActivity**.
1. Substitua a linha que começa com `final static String CLIENT_ID` por:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```java
    > final static String CLIENT_ID = "ENTER_THE_APPLICATION_ID_HERE";
    > ```

    > [!div renderon="docs"]
    > ```java
    > final static String CLIENT_ID = "<ENTER_THE_APPLICATION_ID_HERE>";
    > ```

1. Abra: **aplicativo** > **manifestos** > **AndroidManifest.xml**.
1. Adicione a seguinte atividade ao nó **manifest\application**. Esse snippet de código registra um **BrowserTabActivity** para permitir que o sistema operacional continue executando o aplicativo depois de concluir a autenticação:

    > [!div renderon="docs"]
    > ```xml
    > <!--Intent filter to capture System Browser calling back to our app after Sign In-->
    > <activity
    >     android:name="com.microsoft.identity.client.BrowserTabActivity">
    >     <intent-filter>
    >         <action android:name="android.intent.action.VIEW" />
    >         <category android:name="android.intent.category.DEFAULT" />
    >         <category android:name="android.intent.category.BROWSABLE" />
    > 
    >         <!--Add in your scheme/host from registered redirect URI-->
    >         <!--By default, the scheme should be similar to 'msal[appId]' -->
    >         <data android:scheme="msal<ENTER_THE_APPLICATION_ID_HERE>"
    >             android:host="auth" />
    >     </intent-filter>
    > </activity>
    > ```

    > [!div renderon="portal" class="sxs-lookup"]
    > ```xml
    > <!--Intent filter to capture System Browser calling back to our app after Sign In-->
    > <activity
    >     android:name="com.microsoft.identity.client.BrowserTabActivity">
    >     <intent-filter>
    >         <action android:name="android.intent.action.VIEW" />
    >         <category android:name="android.intent.category.DEFAULT" />
    >         <category android:name="android.intent.category.BROWSABLE" />
    > 
    >         <!--Add in your scheme/host from registered redirect URI-->
    >         <!--By default, the scheme should be similar to 'msal[appId]' -->
    >         <data android:scheme="msalENTER_THE_APPLICATION_ID_HERE"
    >             android:host="auth" />
    >     </intent-filter>
    > </activity>
    > ```

> [!div renderon="docs"]
> <span>6.</span> Substitua `<ENTER_THE_APPLICATION_ID_HERE>` pela *ID do seu aplicativo*. Se você precisar localizar a *ID do aplicativo*, acesse a página *Visão geral*.

## <a name="more-information"></a>Mais informações

Leia as seções a seguir para obter mais informações sobre este início rápido.

### <a name="msal"></a>MSAL

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pelo Azure AD (Microsoft Azure Active Directory). Você pode usar o Gradle para instalá-lo adicionando o seguinte nos **Scripts Gradle** > **build.gradle (módulo: aplicativo)** sob **Dependências**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.1.+'
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Você pode adicionar a referência da MSAL adicionando o seguinte código:

```java
import com.microsoft.identity.client.*;
```

Em seguida, inicialize a MSAL usando o seguinte código:

```java
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        CLIENT_ID);
```

> |Em que: ||
> |---------|---------|
> |`CLIENT_ID` | A ID do aplicativo registrado em *portal.azure.com* |

### <a name="requesting-tokens"></a>Solicitando tokens

A MSAL tem dois métodos para adquirir tokens: `acquireToken` e `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>Obtendo um token de usuário interativamente

Algumas situações exigem forçar os usuários a interagir com o ponto de extremidade do Azure AD v2.0, que resulta em uma alternância de contexto para o navegador do sistema para validar credenciais dos usuários ou para consentimento. Alguns exemplos incluem:

* A primeira vez que os usuários entram no aplicativo
* Quando os usuários precisam reinserir suas credenciais porque a senha expirou
* Quando seu aplicativo está solicitando acesso a um recurso com o qual o usuário precisa consentir
* Quando a autenticação de dois fatores é necessária

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Em que:||
> |---------|---------|
> | `SCOPES` | Contém os escopos que estão sendo solicitados (ou seja, `{ "user.read" }` para o Microsoft Graph ou `{ "<Application ID URL>/scope" }` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Controle de chamada executado quando o controle é dado de volta ao aplicativo após a autenticação |

#### <a name="getting-a-user-token-silently"></a>Obtendo um token de usuário no modo silencioso

Você não desejará exigir que os usuários validem suas credenciais sempre que eles precisarem acessar um recurso. Na maioria das vezes, você quer aquisições e renovação de tokens sem nenhuma interação do usuário. Você pode usar o método `AcquireTokenSilentAsync` para obter tokens para acessar recursos protegidos após o método `acquireToken` inicial:

```java
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

> |Em que:||
> |---------|---------|
> | `SCOPES` | Contém os escopos que estão sendo solicitados (ou seja, `{ "user.read" }` para o Microsoft Graph ou `{ "<Application ID URL>/scope" }` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Controle de chamada executado quando o controle é dado de volta ao aplicativo após a autenticação |

## <a name="next-steps"></a>Próximas etapas

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça as etapas para criar o aplicativo usado neste início rápido

Experimente o tutorial do Android para um guia passo a passo completo sobre a criação de aplicativos e novos recursos, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [Tutorial Chamar a API do Graph para Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL para wiki da biblioteca do Android

Leia mais informações sobre a biblioteca MSAL para Android:

> [!div class="nextstepaction"]
> [MSAL para wiki da biblioteca do Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
