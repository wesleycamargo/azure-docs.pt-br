---
title: Início rápido do JavaScript do Azure AD v2 | Microsoft Docs
description: Saiba como aplicativos JavaScript podem chamar uma API que exige tokens de acesso pelo ponto de extremidade do Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/21/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4c64552ab23331755bf1d292bede61e78b339df0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987417"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Início rápido: conectar os usuários e adquirir um token de acesso de um aplicativo JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Neste início rápido, você aprenderá como usar um exemplo de código que demonstra como um aplicativo de página única (SPA) do JavaScript pode conectar contas corporativas, pessoais e de estudante, obter um token de acesso e chamar a API do Microsoft Graph.

![Como funciona o aplicativo de exemplo gerado por esse Início rápido](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Registre seu aplicativo e faça o download de seu aplicativo de início rápido
>
> ### <a name="step-1-register-your-application"></a>Etapa 1: Registrar seu aplicativo
>
> 1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo.
> 1. No campo **Nome do Aplicativo**, insira um nome para o seu aplicativo.
> 1. Desmarque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.
> 1. Clique em **Adicionar Plataforma** e escolha **Web**.
> 1. Verifique se **Permitir Fluxo Implícito** está *marcado*.
> 1. Em **URLs de Redirecionamento**, adicione `http://localhost:30662/`.
> 1. Clique em **Salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste Início Rápido funcione, você precisa adicionar uma URI de redirecionamento como `http://localhost:30662/` e habilitar o recurso **Concessão implícita**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-javascript/green-check.png) Seu aplicativo já está configurado com esses atributos

#### <a name="step-2-download-the-project"></a>Etapa 2: Baixar o projeto

Você pode escolher a opção mais adequada para seu ambiente de desenvolvimento.
* [Baixar os principais arquivos do projeto para um servidor Web local, como Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip)
* [Baixar o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)

Extraia o arquivo zip para uma pasta local (por exemplo, **C:\Azure-Samples**).

#### <a name="step-3-configure-your-javascript-app"></a>Etapa 3: Configurar o aplicativo JavaScript

> [!div renderon="docs"]
> Edite `msalconfig.js` e substitua `Enter_the_Application_Id_here` pela ID do Aplicativo do aplicativo que você acabou de registrar. Localize a *ID do aplicativo* na página *Visão geral*.

> [!div class="sxs-lookup" renderon="portal"]
> Edite `msalconfig.js` e substitua msalconfig por:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
> [!NOTE]
> Se você usar [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), o URI de redirecionamento será definido como `http://localhost:30662/` conforme estiver configurado no projeto do exemplo de código. Se você usar [Node.js](https://nodejs.org/en/download/) ou qualquer outro servidor Web, defina o redirecionamento de URI como `http://localhost:30662/` e configure o servidor para iniciar a escuta nessa porta.
>

#### <a name="step-4-run-the-project"></a>Etapa 4: Executar o projeto

Se estiver usando o Visual Studio, pressione **F5** para executar seu projeto.

Se estiver usando o Node.js, em uma linha de comando, execute o item a seguir no diretório do projeto para iniciar o servidor:
 ```batch
 npm install
 node server.js
 ```
Abra um navegador da Web e acesse `http://localhost:30662/`. Clique no botão **Chamar a API do Microsoft Graph** para iniciar a entrada.


## <a name="more-information"></a>Mais informações

### <a name="msaljs"></a>*msal.js*

MSAL é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pelo Microsoft Azure Active Directory (Azure AD). O *index.html* do início rápido contém uma referência à biblioteca:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
````

Como alternativa, se tiver o Node instalado, você poderá fazer o download dele pelo npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL

O código de início rápido também mostra como inicializar a biblioteca:

```javascript
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |ID do aplicativo referente ao aplicativo registrado no portal do Azure|
> |`authority`    |É a URL da autoridade. Passar *null* define a autoridade padrão como `https://login.microsoftonline.com/common`. Se seu aplicativo for de locatário único (direcionamento de contas em apenas um diretório), defina esse valor como `https://login.microsoftonline.com/<tenant name or ID>`|
> |`loginCallBack`| O método de retorno de chamada é chamado depois que a autenticação é redirecionada para o aplicativo|
> |`redirectUri`  |A URL à qual os usuários são enviados após a autenticação com o Azure AD|

### <a name="sign-in-users"></a>Conectar usuários

O trecho de código a seguir mostra como realizar a conexão de usuários:

```javascript
userAgentApplication.loginRedirect(graphAPIScopes);
```

> |Where  |  |
> |---------|---------|
> | `graphAPIScopes`   | (Opcional) Contém os escopos que estão sendo solicitados para o consentimento do usuário no momento do logon (por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "api://<Application ID>/access_as_user" ]` para as APIs Web personalizadas). |

> [!TIP]
> Como alternativa, você talvez queira usar o método `loginPopup` para exibir uma janela pop-up para a conexão do usuário.

### <a name="request-tokens"></a>Solicitar tokens

A MSAL tem três métodos usados para adquirir tokens: `acquireTokenRedirect`, `acquireTokenPopup` e `acquireTokenSilent`:

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `acquireTokenSilent` manipula as aquisições e a renovação de tokens sem nenhuma interação do usuário. Após o método `loginRedirect` ou `loginPopup` ser executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter tokens usados para acessar recursos protegidos nas próximas chamadas. As chamadas para solicitar ou renovar tokens são feitas no modo silencioso.

```javascript
// Try to acquire the token used to query Graph API silently first:
userAgentApplication.acquireTokenSilent(graphAPIScopes)
```

> |Where  |  |
> |---------|---------|
> | `graphAPIScopes`   | Contém os escopos que são solicitados para retorno no token de acesso da API (por exemplo, `[ "user.read" ]` para o Microsoft Graph ou `[ "api://<Application ID>/access_as_user" ]` para as APIs Web personalizadas). |

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

 Há situações em que é necessário forçar os usuários a interagir com o ponto de extremidade do Azure AD v2.0. Por exemplo: 
* Os usuários precisam reinserir as credenciais deles, pois a senha expirou
* Seu aplicativo está solicitando acesso a escopos de recursos adicionais com os quais o usuário precisa concordar
* A autenticação de dois fatores é necessária

O padrão usual recomendado para a maioria dos aplicativos é chamar `acquireTokenSilent` primeiro, depois capturar a exceção e, em seguida, chamar `acquireTokenRedirect` (ou `acquireTokenPopup`) para iniciar uma solicitação interativa.

Chamar o `acquireTokenRedirect(scope)` resulta no redirecionamento dos usuários para o ponto de extremidade do Azure AD v2.0 (ou `acquireTokenPopup(scope)` resulta em uma janela pop-up) em que os usuários precisam interagir confirmando as credenciais deles ou dando consentimento para o recurso necessário, ou ainda concluindo a autenticação de dois fatores.

```javascript
userAgentApplication.acquireTokenRedirect(graphAPIScopes);
```

## <a name="next-steps"></a>Próximas etapas

Para ver um guia passo a passo com mais detalhes sobre como compilar o aplicativo para este início rápido, experimente o seguinte tutorial do JavaScript.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Conheça as etapas para criar o aplicativo usado neste início rápido

> [!div class="nextstepaction"]
> [Tutorial Chamar a API do Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Localize o repositório MSAL para ver documentação, Perguntas Frequentes, problemas e muito mais

> [!div class="nextstepaction"]
> [Repositório GitHub msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
