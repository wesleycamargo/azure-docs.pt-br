---
title: Início rápido do JavaScript do Azure AD v2 | Microsoft Docs
description: Saiba como aplicativos JavaScript podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a97e03f3c195b9fbd0ee7a09950414b7a940c7c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217472"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Início Rápido: Conectar os usuários e adquirir um token de acesso de um aplicativo JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Neste início rápido, você aprenderá a usar um exemplo de código que demonstra como um SPA (aplicativo de página única) do JavaScript pode conectar contas empresariais, pessoais e de estudante e obter um token de acesso para chamar a API do Microsoft Graph ou qualquer API Web.

![Como funciona o aplicativo de exemplo gerado por esse Início rápido](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Vá para o [portal do Azure – Registro de Aplicativo (Versão Prévia)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Insira um nome para o aplicativo e clique em **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> 1. Faça login no [Portal do Azure](https://portal.azure.com/) para registrar um aplicativo.
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
> 1. No painel de navegação à esquerda, selecione o serviço do **Azure Active Directory** e, em seguida, selecione **Registros de aplicativo (Visualizar)> Novo registro**.
> 1. Quando a página **Registrar um aplicativo** aparecer, insira um nome para o seu aplicativo.
> 1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
> 1. Selecione a plataforma **Web** na seção **URI de Redirecionamento** e defina o valor como `http://localhost:30662/`.
> 1. Ao terminar, selecione **Registrar**.  Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)**.
> 1. Este início rápido requer que o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja ativado. No painel de navegação à esquerda do aplicativo registrado, selecione **Autenticação**.
> 1. Em **Configurações avançadas**, sob **Concessão implícita**, marque as caixas de seleção **Tokens de ID** e **Tokens de Acesso**. Os tokens de ID e tokens de acesso são necessários, pois esse aplicativo precisa fazer logon dos usuários e chamar uma API.
> 1. Clique em **Salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste Início Rápido funcione, você precisa adicionar uma URI de redirecionamento como `http://localhost:30662/` e habilitar o recurso **Concessão implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-javascript/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-the-project"></a>Etapa 2: Baixe o projeto

Você pode escolher a opção mais adequada para seu ambiente de desenvolvimento.
* [Baixar os principais arquivos do projeto para um servidor Web local, como Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Baixar o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Extraia o arquivo zip para uma pasta local, por exemplo, **C:\Azure-Samples**.

#### <a name="step-3-configure-your-javascript-app"></a>Etapa 3: Configurar o aplicativo JavaScript

> [!div renderon="docs"]
> Edite `index.html` e defina o `clientID` e `authority` valores abaixop `applicationConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Edite `index.html` e substitua `applicationConfig` por:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!div renderon="docs"]
>
> Em que:
> - `Enter_the_Application_Id_here` - é a **ID do aplicativo (cliente)** que você registrou.
> - `Enter_the_Tenant_Info_Here` - é definido como uma das seguintes opções:
>   - Se seu aplicativo dá suporte a **Contas neste diretório organizacional**, substitua esse valor pela **ID do Locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>   - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`
>   - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**, substitua esse valor por `common`
>
> > [!TIP]
> > Para encontrar os valores de **ID do aplicativo (cliente)**, **ID de diretório (locatário)** e **Tipos de conta com suporte**, vá para a página **Visão Geral** do aplicativo no portal do Azure.

> [!NOTE]
> O servidor está configurado para escutar na porta 30662 no arquivo *server.js* no projeto [Node.js](https://nodejs.org/en/download/) e no arquivo *.csproj* no [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) projeto.
>

#### <a name="step-4-run-the-project"></a>Etapa 4: Executar o projeto

* Se você estiver usando o Node.js:

    1. Execute o seguinte comando no diretório do projeto para iniciar o servidor:

        ```batch
        npm install
        node server.js
        ```

    1. Abra um navegador da Web e acesse `http://localhost:30662/`.
    1. Clique no botão **Entrar** para iniciar a entrada e depois chamar a API do Microsoft Graph.


* Se você estiver usando o Visual Studio, certifique-se de selecionar a solução de projeto e pressione **F5** para executar o projeto.

## <a name="more-information"></a>Mais informações

### <a name="msaljs"></a>*msal.js*

MSAL é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pelo Microsoft Azure Active Directory (Azure AD). O *index.html* do início rápido contém uma referência à biblioteca:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

Como alternativa, se tiver o Node instalado, você poderá fazer o download dele pelo npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código de início rápido também mostra como inicializar a biblioteca:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |ID do aplicativo referente ao aplicativo registrado no portal do Azure|
> |`authority`    |É a URL da autoridade. Passar *null* define a autoridade padrão como `https://login.microsoftonline.com/common`. Se seu aplicativo for de locatário único (direcionamento de contas em apenas um diretório), defina esse valor como `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| O método de retorno de chamada é solicitado depois que a autenticação é redirecionada para o aplicativo. Aqui, passa-se `acquireTokenRedirectCallBack`. Isso será nulo se usar loginPopup.|
> |`options`  |Uma coleção de parâmetros opcionais. Nesse caso `storeAuthStateInCookie` e `cacheLocation` são a configuração opcional. Consulte a [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) para obter mais detalhes sobre as opções. |

### <a name="sign-in-users"></a>Conectar usuários

O trecho de código a seguir mostra como realizar a conexão de usuários:

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | (Opcional) Contém os escopos que estão sendo solicitados para o consentimento do usuário no momento do logon. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`). Aqui, passa-se `applicationConfig.graphScopes`. |

> [!TIP]
> Como alternativa, talvez você queira usar o método `loginRedirect` para redirecionar a página atual para a página de entrada, em vez de uma janela pop-up.

### <a name="request-tokens"></a>Solicitar tokens

A MSAL tem três métodos usados para adquirir tokens: `acquireTokenRedirect`, `acquireTokenPopup` e `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `acquireTokenSilent` manipula as aquisições e a renovação de tokens sem nenhuma interação do usuário. Após o método `loginRedirect` ou `loginPopup` ser executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter tokens usados para acessar recursos protegidos nas próximas chamadas. As chamadas para solicitar ou renovar tokens são feitas no modo silencioso.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | Contém os escopos solicitados para retorno no token de acesso da API. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`). Aqui, passa-se `applicationConfig.graphScopes`.|

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

Há situações em que é necessário forçar os usuários a interagir com o ponto de extremidade do Azure AD v2.0. Por exemplo: 
* Os usuários precisam reinserir as credenciais deles, pois a senha expirou
* Seu aplicativo está solicitando acesso a escopos de recursos adicionais com os quais o usuário precisa concordar
* A autenticação de dois fatores é necessária

O padrão usual recomendado para a maioria dos aplicativos é chamar `acquireTokenSilent` primeiro, depois capturar a exceção e, em seguida, chamar `acquireTokenRedirect` (ou `acquireTokenPopup`) para iniciar uma solicitação interativa.

Chamar o `acquireTokenPopup(scope)` resulta em uma janela pop-up para entrar (ou `acquireTokenRedirect(scope)` resulta no redirecionamento dos usuários para o ponto de extremidade do Azure AD v2.0) em que os usuários precisam interagir confirmando as credenciais deles ou fornecendo consentimento para o recurso necessário, ou ainda concluindo a autenticação de dois fatores.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> Esse início rápido usa os métodos `loginRedirect` e `acquireTokenRedirect` quando o navegador usado é o Internet Explorer devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado à manipulação de janelas pop-up pelo navegador Internet Explorer.

## <a name="next-steps"></a>Próximas etapas

Para ver um guia passo a passo com mais detalhes sobre como compilar o aplicativo para este início rápido, experimente o seguinte tutorial do JavaScript.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Conheça as etapas para criar o aplicativo usado neste início rápido

> [!div class="nextstepaction"]
> [Tutorial Chamar a API do Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Localize o repositório MSAL para ver documentação, Perguntas Frequentes, problemas e muito mais

> [!div class="nextstepaction"]
> [Repositório GitHub msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
