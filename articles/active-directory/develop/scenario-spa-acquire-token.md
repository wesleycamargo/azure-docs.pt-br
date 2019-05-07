---
title: Aplicativo de página única (adquirir um token para chamar uma API) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de página única (adquirir um token para chamar uma API)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138808"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Aplicativo de página única - adquirir um token para chamar uma API

O padrão para adquirir tokens para APIs com msal é a primeira tentativa de uma solicitação de token silenciosa usando o `acquireTokenSilent` método. Quando este método é chamado, a biblioteca primeiro verifica o cache no armazenamento do navegador para ver se existe um token válido e o retorna. Quando não houver nenhum token válido no cache, ele envia uma solicitação de token silenciosa ao Azure Active Directory (Azure AD) de um iframe oculto. Esse método também permite que a biblioteca Renovar tokens. Para obter mais detalhes sobre a sessão de logon único e valores de vida útil do token do AD do Azure, consulte [tempos de vida de token](active-directory-configurable-token-lifetimes.md).

As solicitações de token silenciosas para o Azure AD podem falhar por algum motivo, como um sessão AD do Azure expirado ou uma alteração de senha. Nesse caso, você pode chamar um dos métodos interativos (que solicitarão ao usuário) para obter tokens.

* [Adquirir o token com uma janela pop-up](#acquire-token-with-a-pop-up-window) usando `acquireTokenPopup`
* [Adquirir o token com redirecionamento](#acquire-token-with-redirect) usando `acquireTokenRedirect`

**Escolhendo entre uma experiência de redirecionamento ou pop-up**

 Você não pode usar uma combinação dos métodos de pop-up e de redirecionamento em seu aplicativo. A escolha entre uma experiência de redirecionamento ou pop-up depende de seu fluxo de aplicativo.

* Se você não quiser que o usuário navegar para fora de sua página principal do aplicativo durante a autenticação, é recomendável usar os métodos de pop-up. Uma vez que o redirecionamento de autenticação ocorre em uma janela pop-up, o estado do aplicativo principal é preservado.

* Há alguns casos em que você pode precisar usar os métodos de redirecionamento. Se os usuários do seu aplicativo tiverem restrições de navegador ou as políticas de onde as janelas pop-ups estão desabilitadas, você pode usar os métodos de redirecionamento. Também é recomendável usar os métodos de redirecionamento com o navegador Internet Explorer, pois há determinadas [problemas conhecidos com o Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) ao manipular janelas pop-up.

Você pode definir os escopos de API que você deseja que o token de acesso para incluir ao criar a solicitação de token de acesso. Observe que todos os escopos solicitados não pode ser concedido no token de acesso e depende do consentimento do usuário.

## <a name="acquire-token-with-a-pop-up-window"></a>Adquirir o token com uma janela pop-up

### <a name="javascript"></a>JavaScript

O padrão acima usando os métodos para uma experiência de pop-up:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

O wrapper MSAL Angular fornece a conveniência de adicionar o interceptador HTTP `MsalInterceptor` automaticamente qual adquirir tokens de acesso silenciosamente e anexá-los às solicitações HTTP para APIs.

Você pode especificar os escopos para APIs no `protectedResourceMap` opção de configuração que o MsalInterceptor solicitará ao adquirir automaticamente tokens.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

Para êxito e falha da aquisição de token silenciosa, MSAL Angular fornece retornos de chamada que você pode assinar. Também é importante lembrar-se de cancelar a assinatura.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Como alternativa, você pode adquirir explicitamente tokens usando os métodos de token de aquisição, conforme descrito na biblioteca msal principal.

## <a name="acquire-token-with-redirect"></a>Adquirir o token com redirecionamento

### <a name="javascript"></a>JavaScript

O padrão é como descrito acima, mas mostrado com um método de redirecionamento para adquirir o token de forma interativa. Observe que você precisa registrar o retorno de chamada de redirecionamento, conforme mencionado acima.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

### <a name="angular"></a>Angular

Isso é o mesmo descrito acima.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamar uma API Web](scenario-spa-call-api.md)
