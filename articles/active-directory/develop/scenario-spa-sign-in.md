---
title: Aplicativo de página única (entrar) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de página única (entrar)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138795"
---
# <a name="single-page-application---sign-in"></a>Aplicativo de página única - entrar

Saiba como adicionar a entrada para o código para seu aplicativo de página única.

Antes de poder obter tokens para acessar as APIs em seu aplicativo, você precisará de um contexto de usuário autenticado. Você pode conectar usuários ao seu aplicativo em msal de duas maneiras:

* [Entrar com uma janela pop-up](#sign-in-with-a-pop-up-window) usando `loginPopup` método
* [Entrar com redirecionamento](#sign-in-with-redirect) usando `loginRedirect` método

Você também pode passar os escopos das APIs em que é necessário o consentimento no momento da entrada do usuário.

> [!NOTE]
> Se seu aplicativo já tem acesso a um contexto de usuário autenticado ou id de token, você pode ignorar a etapa de logon e adquirir diretamente de tokens. Para obter mais detalhes, consulte [sso sem logon msal](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Escolhendo entre uma experiência de redirecionamento ou pop-up

Você não pode usar uma combinação dos métodos de pop-up e de redirecionamento em seu aplicativo. A escolha entre uma experiência de redirecionamento ou pop-up depende de seu fluxo de aplicativo.

* Se você não quiser que o usuário navegar para fora de sua página principal do aplicativo durante a autenticação, é recomendável usar os métodos de pop-up. Uma vez que o redirecionamento de autenticação ocorre em uma janela pop-up, o estado do aplicativo principal é preservado.

* Há alguns casos em que você pode precisar usar os métodos de redirecionamento. Se os usuários do seu aplicativo tiverem restrições de navegador ou as políticas de onde as janelas pop-ups estão desabilitadas, você pode usar os métodos de redirecionamento. Use os métodos de redirecionamento com o navegador Internet Explorer, pois há determinadas [problemas conhecidos com o Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) ao manipular janelas pop-up.

## <a name="sign-in-with-a-pop-up-window"></a>Entrar com uma janela pop-up

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

O wrapper MSAL Angular permite proteger rotas específicas em seu aplicativo simplesmente adicionando o `MsalGuard` à definição de rota. Essa protetor invocará o método de entrada quando essa rota é acessada.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Para obter uma experiência de janela pop-up, habilite o `popUp` opção config. Você também pode passar os escopos que exigem o consentimento da seguinte maneira:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Entrar com redirecionamento

### <a name="javascript"></a>JavaScript

Os métodos de redirecionamento não retornam uma promessa devido a navegação para fora do aplicativo principal. Para processar e os retornados tokens de acesso, você precisará registrar retornos de chamada de êxito e erro antes de chamar os métodos de redirecionamento.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

O código aqui é o mesmo descrito acima na entrar com uma seção da janela pop-up. O fluxo padrão é de redirecionamento.

> [!NOTE]
> O token de ID não contém os escopos consentidos e representa apenas o usuário autenticado. Os escopos consentidos são retornados no token de acesso que você vai adquirir na próxima etapa.

## <a name="sign-out"></a>Sair

A biblioteca MSAL fornece um `logout` método que limpa o cache no armazenamento do navegador e envia uma solicitação de saída ao Azure AD. Depois de sair, ele redireciona de volta para a página inicial do aplicativo por padrão.

Você pode configurar o URI ao qual ele deve redirecionar após o sinal de fora, definindo o `postLogoutRedirectUri`. Esse URI também deve ser registrado como o URI de Logout no registro do aplicativo.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo](scenario-spa-acquire-token.md)
