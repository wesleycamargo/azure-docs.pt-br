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
ms.date: 04/01/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd78e6acd801f3b973cc45609b72f86b257f4d43
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862753"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início Rápido: Entrada de usuários e chamada à API do Microsoft Graph em um aplicativo Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Este início rápido contém um exemplo de código que demonstra como um aplicativo Android pode conectar contas corporativas, pessoais ou de estudante, obter um token de acesso e chamar a API do Microsoft Graph.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-android/android-intro-updated.png)

> [!NOTE]
> **Pré-requisitos**
> * Android Studio 3+
> * É necessário ter o Android 21+ 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo,
> 1. Vá para o [portal do Azure – Registro de Aplicativo (Versão Prévia)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs).
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
> 1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e selecione **Registros de aplicativo (Versão prévia)** > **Novo registro**.
> 1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
>      - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `Android-Quickstart`.
>      - Pressione o `Register` botão.
> 1. Acesse `Authentication` > `Redirect URIs` > `Suggested Redirect URIs for public clients` e selecione o URI de Redirecionamento de formato **msal{AppId}://auth**. Salve a alteração.


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Etapa 1: Configurar seu aplicativo
> Para o exemplo de código para este início rápido funcionar, você precisará adicionar uma URL de resposta como **msal{AppId}://auth** (em que {AppId} é a ID do aplicativo do seu aplicativo).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-android/green-check.png) Seu aplicativo já está configurado com esses atributos

#### <a name="step-2-download-the-project"></a>Etapa 2: Baixe o projeto

* [Baixar o projeto do Android Studio](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Etapa 3: Configurar seu projeto

> [!div renderon="docs"]
> Se você tiver selecionado a Opção 1 acima, poderá ignorar essas etapas. Abra o projeto no Android Studio e execute o aplicativo. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extraia e abra o projeto no Android Studio.
> 1. Dentro de **app** > **res** > **raw**, abra **auth_config.json**.
> 1. Edite **auth_config.json** e substitua o `client_id` e o `tenant_id`:
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. Dentro de **app** > **manifests**, abra **AndroidManifest.xml**.
> 1. Adicione a seguinte atividade ao nó **manifest\application**. Este código permite que a Microsoft retorne a chamada para seu aplicativo:   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. Extraia e abra o projeto no Android Studio.
> 1. Dentro de **app** > **res** > **raw**, abra **auth_config.json**.
> 1. Edite **auth_config.json** e substitua o `client_id` e o `redirect_uri`:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Dentro de **app** > **manifests**, abra **AndroidManifest.xml**.
> 1. Adicione a seguinte atividade ao nó **manifest\application**. Esse snippet de código registra um **BrowserTabActivity** para permitir que o sistema operacional continue executando o aplicativo depois de concluir a autenticação:
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Substitua `<ENTER_THE_APPLICATION_ID_HERE>` pela *ID do seu aplicativo*. Se você precisar localizar a *ID do aplicativo*, acesse a página *Visão geral*.

## <a name="more-information"></a>Mais informações

Leia as seções a seguir para obter mais informações sobre este início rápido.

### <a name="msal"></a>MSAL

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pelo Azure AD (Microsoft Azure Active Directory). Você pode usar o Gradle para instalá-lo adicionando o seguinte nos **Scripts Gradle** > **build.gradle (módulo: aplicativo)** sob **Dependências**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
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
        R.raw.auth_config);
```

> |Em que: ||
> |---------|---------|
> |`R.raw.auth_config` | Esse arquivo contém as configurações para seu aplicativo, incluindo a ID do aplicativo/cliente, Conectar público-alvo e várias outras opções de personalização. |

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
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |Em que:||
> |---------|---------|
> | `SCOPES` | Contém os escopos que estão sendo solicitados (ou seja, `{ "user.read" }` para o Microsoft Graph ou `{ "<Application ID URL>/scope" }` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`) |
> | `accounts.get(0)` | Contém a conta para a qual você está tentando obter tokens silenciosamente |
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
