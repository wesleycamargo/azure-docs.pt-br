---
title: Aplicativo de página única (configuração do código do aplicativo) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de página única (configuração de código do aplicativo)
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
ms.openlocfilehash: b71454fc553a0f81c26426a6a9588f15d5311e38
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406430"
---
# <a name="single-page-application---code-configuration"></a>Aplicativo de página única - configuração de código

Saiba como configurar o código para seu aplicativo de página única (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Bibliotecas MSAL que dão suporte a fluxo implícito

Plataforma de identidade da Microsoft fornece a biblioteca msal para suportar o fluxo implícito usando a indústria recomendado práticas seguras.  

As bibliotecas que dão suporte a fluxo implícito são:

| Biblioteca MSAL | Descrição |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Biblioteca de JavaScript simples para uso em qualquer aplicativo de web do lado cliente criado usando estruturas de JavaScript ou SPA como Angular, VUE, React. js, etc. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper da biblioteca principal msal para simplificar o uso em aplicativos de página única criado com a estrutura Angular. Essa biblioteca está em versão prévia e tem [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) com determinadas versões Angular e navegadores. |

## <a name="application-code-configuration"></a>Configuração de código do aplicativo

Na biblioteca MSAL, as informações de registro de aplicativo são passadas como a configuração durante a inicialização da biblioteca.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Para obter mais detalhes sobre as opções configuráveis disponíveis, consulte [Inicializando o aplicativo com a msal](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientId: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entrada e saída](scenario-spa-sign-in.md)
