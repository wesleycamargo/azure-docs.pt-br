---
title: Logon único (biblioteca de autenticação do Microsoft para JavaScript) | Azure
description: Saiba mais sobre a criação de experiências de logon únicas usando a biblioteca de autenticação da Microsoft para JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0fb0731b7ac46210294e3766b33bd6a239dcc2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075844"
---
# <a name="single-sign-on-with-msaljs"></a>Logon único com o msal

Single Sign-On (SSO) permite que os usuários insiram suas credenciais de uma vez para entrar e estabelecer uma sessão que pode ser reutilizada em vários aplicativos sem a necessidade de se autenticar novamente. Isso fornece uma experiência perfeita ao usuário e reduz o repetidas solicitará as credenciais.

Azure AD fornece recursos de SSO para aplicativos, definindo um cookie de sessão quando o usuário se autentica na primeira vez. A biblioteca msal permite que os aplicativos para utilizá-lo de algumas maneiras.

## <a name="sso-between-browser-tabs"></a>SSO entre as guias do navegador

Quando seu aplicativo é aberto em várias guias e entrar pela primeira vez o usuário em uma guia, o usuário está conectado também nas outras guias sem ser solicitado. Msal armazena em cache o token de ID para o usuário no navegador `localStorage` e se conectará o usuário para o aplicativo nas outras guias abertos.

Por padrão, usa a msal `sessionStorage` que não permite a sessão seja compartilhado entre as guias. Para obter o SSO entre as guias, certifique-se de definir as `cacheLocation` na msal para `localStorage` conforme mostrado abaixo.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO entre aplicativos

Quando um usuário é autenticado, um cookie de sessão é definido no domínio do Azure AD no navegador. Msal se baseia nesse cookie de sessão para fornecer o SSO para o usuário entre diferentes aplicativos. Msal também armazena em cache os tokens de ID e tokens de acesso do usuário no armazenamento do navegador por domínio de aplicativo. Como resultado, o comportamento SSO varia para casos diferentes:  

### <a name="applications-on-the-same-domain"></a>Aplicativos no mesmo domínio

Quando aplicativos são hospedados no mesmo domínio, o usuário pode entrar em um aplicativo uma vez e, em seguida, é autenticado para outros aplicativos sem um aviso. Msal aproveita os tokens em cache para o usuário no domínio fornecer o SSO.

### <a name="applications-on-different-domain"></a>Aplicativos em um domínio diferente

Quando aplicativos são hospedados em diferentes domínios, os tokens em cache em um domínio não podem ser acessados pelo msal no domínio B.

Isso significa que quando os usuários conectados no domínio navega para um aplicativo no domínio B, eles serão redirecionados ou solicitados com a página do Azure AD. Uma vez que o Azure AD ainda tem o cookie de sessão do usuário, ele será a entrada do usuário e não será necessário reinserir as credenciais. Se o usuário tiver várias contas de usuário na sessão com o Azure AD, o usuário será solicitado a escolher a conta relevante para entrar com.

### <a name="automatically-select-account-on-azure-ad"></a>Selecionar automaticamente a conta no AD do Azure

Em alguns casos, o aplicativo tem acesso ao contexto de autenticação do usuário e deseja evitar a solicitação de seleção de conta do Azure AD quando várias contas estão conectadas.  Isso pode ser feito de várias maneiras diferentes:

**Usando a ID de sessão (SID)**

ID da sessão é uma [declaração opcional](active-directory-optional-claims.md) que pode ser configurado nos tokens de ID. Esta declaração permite que o aplicativo para identificar Azure do usuário sessão AD independente do nome da conta do usuário ou nome de usuário. Você pode passar os parâmetros de solicitação para o SID a `acquireTokenSilent` chamar. Isso permitirá que o Azure AD ignorar a seleção de conta. SID está associado ao cookie de sessão e não irá cruzar os contextos de navegador.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID pode ser usado somente com solicitações de autenticação sem confirmação feitas por `acquireTokenSilent` chamar em msal.
Você pode encontrar as etapas para configurar declarações opcionais no manifesto do aplicativo [aqui](active-directory-optional-claims.md).

**Usando a dica de logon**

Se você não tiver o SID de declaração configurado ou precisar ignorar o aviso de seleção de conta em chamadas de autenticação interativa, você pode fazer isso, fornecendo uma `login_hint` nos parâmetros de solicitação e, opcionalmente, um `domain_hint` como `extraQueryParameters` na msal métodos interativos (`loginPopup`, `loginRedirect`, `acquireTokenPopup` e `acquireTokenRedirect`). Por exemplo: 

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Você pode obter os valores para login_hint e domain_hint lendo as declarações retornadas no token de ID para o usuário.

* **loginHint** deve ser definido como o `preferred_username` de declaração no token de ID.

* **domain_hint** só é necessário para ser passado ao usar /common autoridade. A dica de domínio é determinada pelo locatário ID(tid).  Se o `tid` declaração no token de ID é `9188040d-6c67-4c5b-b112-36a304b66dad` é consumidores. Caso contrário, é que as organizações.

Leia [aqui](v2-oauth2-implicit-grant-flow.md) para obter mais informações sobre os valores de dica de logon e a dica de domínio.

> [!Note]
> Você não pode passar o SID e login_hint ao mesmo tempo. Isso resulta na resposta de erro.

## <a name="sso-without-msaljs-login"></a>SSO sem logon msal

Por design, a msal requer que um método de logon é chamado para estabelecer um contexto de usuário antes de obter tokens para APIs. Como os métodos de logon são interativos, o usuário verá um prompt.

Há alguns casos em que os aplicativos têm acesso ao contexto do usuário autenticado ou token de ID por meio da autenticação iniciada em outro aplicativo e quiser aproveitar o SSO para adquirir tokens sem precisar primeiro entrar por meio da msal.

Um exemplo disso é: Um usuário está conectado a um aplicativo web de pai que hospeda o outro aplicativo de JavaScript em execução como um complemento ou plug-in.

Nesse cenário, a experiência de SSO pode ser obtida da seguinte maneira:

Passe o `sid` se estiver disponível (ou `login_hint` e, opcionalmente `domain_hint`) como parâmetros para a msal da solicitação `acquireTokenSilent` chamar da seguinte maneira:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO no adal. js para atualização da msal

Msal traz a paridade de recursos com adal. js para cenários de autenticação do AD do Azure. Para facilitar a migração da adal. js para msal e evitar avisar os usuários para entrar novamente, a biblioteca lê o token de ID que representa a sessão do usuário no cache da adal. js e conecta o usuário na msal perfeitamente.  

Para aproveitar o comportamento de logon único (SSO), ao atualizar do adal. js, você precisará garantir que as bibliotecas estão usando `localStorage` no caching de tokens. Defina as `cacheLocation` para `localStorage` na configuração o msal e o adal. js na inicialização da seguinte maneira:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Quando isso estiver configurado, a msal será capaz de ler o estado armazenado em cache do usuário autenticado no adal. js e usá-la para fornecer o SSO em msal.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [única sessão de logon e a vida útil do token](active-directory-configurable-token-lifetimes.md) valores no Azure AD.
