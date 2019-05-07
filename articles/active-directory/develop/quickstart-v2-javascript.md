---
title: Início rápido do JavaScript para a plataforma de identidade da Microsoft | Azure
description: Saiba como aplicativos do JavaScript podem chamar uma API que exige tokens de acesso pela plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f242afb717557a35b81515ab718971bdc398b5a
ms.sourcegitcommit: abeefca6cd5ca01c3e0b281832212aceff08bf3e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "64992785"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application-spa"></a>Início Rápido: Conectar usuários e adquirir um token de acesso de um SPA (aplicativo de página única) do JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Neste início rápido, você aprenderá a usar um exemplo de código que demonstra como um SPA (aplicativo de página única) do JavaScript pode conectar contas empresariais, pessoais e de estudante e obter um token de acesso para chamar a API do Microsoft Graph ou qualquer API Web.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Pré-requisitos

A seguinte configuração será necessária para este início rápido:
* Para executar o projeto com um servidor node.js,
    * Instale o [Node.js](https://nodejs.org/en/download/)
    * Instale o [Visual Studio Code](https://code.visualstudio.com/download) para editar os arquivos de projeto
* Para executar o projeto como uma solução do Visual Studio, instale o [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
> 1. Acesse o novo painel do [portal do Azure – Registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Insira um nome para o aplicativo e clique em **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
> 1. Navegue até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
> 1. Selecione **Novo registro**.
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
* [Baixe os principais arquivos de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para executar com um servidor Web que usa o Node.js. Para abrir os arquivos, use um editor como [Visual Studio Code](https://code.visualstudio.com/).

* (Opcional) [Baixe o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip), para executar com o servidor IIS. Extraia o arquivo zip para uma pasta local, por exemplo, **C:\Azure-Samples**.



#### <a name="step-3-configure-your-javascript-app"></a>Etapa 3: Configurar o aplicativo JavaScript

> [!div renderon="docs"]
> Na pasta *JavaScriptSPA*, edite `index.html` e defina os valores `clientID` e `authority` em `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Na pasta *JavaScriptSPA*, edite `index.html` e substitua `msalConfig` por:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="docs"]
>
> Em que:
> - `Enter_the_Application_Id_here` - é a **ID do aplicativo (cliente)** que você registrou.
> - `Enter_the_Tenant_Info_Here` - é definido como uma das seguintes opções:
>   - Se seu aplicativo dá suporte a **Contas neste diretório organizacional**, substitua esse valor pela **ID do Locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>   - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`
>   - Se o seu aplicativo der suporte a **Contas em qualquer diretório organizacional e contas pessoais Microsoft**, substitua esse valor por `common`. Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por `consumers`.
>
> > [!TIP]
> > Para encontrar os valores de **ID do aplicativo (cliente)**, **ID de diretório (locatário)** e **Tipos de conta com suporte**, vá para a página **Visão Geral** do aplicativo no portal do Azure.
>

#### <a name="step-4-run-the-project"></a>Etapa 4: Executar o projeto

* Se você estiver usando [Node.js](https://nodejs.org/en/download/):

    1. Execute o seguinte comando no diretório do projeto para iniciar o servidor:

        ```batch
        npm install
        node server.js
        ```

    1. Abra um navegador da Web e acesse `http://localhost:30662/`.
    1. Clique no botão **Entrar** para iniciar a entrada e depois chamar a API do Microsoft Graph.


* Se estiver usando [Visual Studio](https://visualstudio.microsoft.com/downloads/), certifique-se de selecionar a solução do projeto e, em seguida, pressione **F5** para executar o projeto.

Depois que o navegador carregar o aplicativo, clique em **Entrar**.  Na primeira vez em que entrar, será solicitado que você forneça seu consentimento para permitir que o aplicativo acesse seu perfil e faça logon. Ao entrar com êxito, você verá as informações do seu perfil do usuário exibidas na página.

## <a name="more-information"></a>Mais informações

### <a name="msaljs"></a>*msal.js*

MSAL é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. O *index.html* do início rápido contém uma referência à biblioteca:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0-preview.4/js/msal.min.js"></script>
```
> [!TIP]
> É possível substituir a versão acima da versão lançada mais recente nas [versões MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Como alternativa, se você tiver instalado o Node, poderá baixar a versão prévia mais recente por meio do npm:

```batch
npm install msal@preview
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código de início rápido também mostra como inicializar a biblioteca:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |ID do aplicativo referente ao aplicativo registrado no portal do Azure|
> |`authority`    | (Opcional) É a URL de autoridade conforme descrito na seção de configuração acima para dar suporte a tipos de conta. A autoridade padrão é `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Opcional) Isso define o armazenamento do navegador para o estado de autenticação. O padrão é sessionStorage.   |
> |`storeAuthStateInCookie`  | (Opcional) A biblioteca armazenará o estado de solicitação de autenticação necessário para a validação dos fluxos de autenticação nos cookies do navegador. Isso é definido para os navegadores IE e Edge mitigarem certos [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

 Confira a [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) para obter mais detalhes sobre as opções configuráveis disponíveis.

### <a name="sign-in-users"></a>Conectar usuários

O trecho de código a seguir mostra como realizar a conexão de usuários:

```javascript
var request = {
    scopes: ["user.read"]
};

myMSALObj.loginPopup(request).then(function (loginResponse) {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | (Opcional) Contém os escopos que estão sendo solicitados para o consentimento do usuário no momento do logon. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`). |

> [!TIP]
> Como alternativa, talvez você queira usar o método `loginRedirect` para redirecionar a página atual para a página de entrada, em vez de uma janela pop-up.

### <a name="request-tokens"></a>Solicitar tokens

A MSAL tem três métodos usados para adquirir tokens: `acquireTokenRedirect`, `acquireTokenPopup` e `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `acquireTokenSilent` manipula as aquisições e a renovação de tokens sem nenhuma interação do usuário. Após o método `loginRedirect` ou `loginPopup` ser executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter tokens usados para acessar recursos protegidos nas próximas chamadas. As chamadas para solicitar ou renovar tokens são feitas no modo silencioso.

```javascript
var request = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenSilent(request).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | Contém os escopos solicitados para retorno no token de acesso da API. Por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

Há situações em que é necessário forçar os usuários a interagir com o ponto de extremidade da plataforma de identidade da Microsoft. Por exemplo: 
* Os usuários precisam reinserir as credenciais deles, pois a senha expirou
* Seu aplicativo está solicitando acesso a escopos de recursos adicionais com os quais o usuário precisa concordar
* A autenticação de dois fatores é necessária

O padrão usual recomendado para a maioria dos aplicativos é chamar `acquireTokenSilent` primeiro, depois capturar a exceção e, em seguida, chamar `acquireTokenPopup` (ou `acquireTokenRedirect`) para iniciar uma solicitação interativa.

Chamar o `acquireTokenPopup` resulta em uma janela pop-up para entrar (ou `acquireTokenRedirect` resulta no redirecionamento dos usuários para o ponto de extremidade da plataforma de identidade da Microsoft) em que os usuários precisam interagir confirmando as credenciais deles ou fornecendo consentimento para o recurso necessário, ou ainda concluindo a autenticação de dois fatores.

```javascript
var request = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenPopup(request).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```
> [!NOTE]
> Este início rápido usa os métodos `loginRedirect` e `acquireTokenRedirect` quando o navegador usado é o Internet Explorer devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado À manipulação de janelas pop-up pelo navegador Internet Explorer.

## <a name="next-steps"></a>Próximas etapas

Para ver um guia passo a passo com mais detalhes sobre como compilar o aplicativo para este início rápido, experimente o seguinte tutorial do JavaScript.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Conheça as etapas para criar o aplicativo usado neste início rápido

> [!div class="nextstepaction"]
> [Tutorial para entrar e chamar o MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Localize o repositório MSAL para ver documentação, Perguntas Frequentes, problemas e muito mais

> [!div class="nextstepaction"]
> [Repositório GitHub do MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
