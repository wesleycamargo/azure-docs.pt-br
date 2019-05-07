---
title: Aplicativo de página única (chamada de uma API da web) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de página única (chamar uma API da web)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f141a5374c0e794b264f6e0135ca3e15ff8359
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074839"
---
# <a name="single-page-application---call-a-web-api"></a>Aplicativo de página única - chamada de uma API da web

É recomendável que você chame o `acquireTokenSilent` método para adquirir ou renovar um token de acesso antes de chamar uma API da web. Quando você tiver um token, você pode chamar uma API web protegida.

## <a name="call-a-web-api"></a>Chamar uma API da Web

### <a name="javascript"></a>JavaScript

Use o token de acesso adquirido como um portador em uma solicitação HTTP para chamar qualquer API da web, como a API do Microsoft Graph. Por exemplo: 

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

### <a name="angular"></a>Angular

Conforme mencionado na [ao adquirir a seção tokens](scenario-spa-acquire-token.md), o wrapper MSAL Angular aproveita o interceptador HTTP para adquirir tokens de acesso silenciosamente e anexá-los às solicitações HTTP para APIs automaticamente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para a produção](scenario-spa-production.md)
