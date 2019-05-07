---
title: API da Web que chama web APIs (chamada de APIs) – plataforma de identidade da Microsoft
description: Saiba como criar uma API que chamadas downstream APIs da web (chamando uma API da web) da web.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074719"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>API da Web que chama APIs - web chamar uma API

Quando você tiver um token, você pode chamar uma API web protegida. Isso é feito do controlador de sua API da web ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Código do controlador

Aqui está a continuação do exemplo de código mostrado na [APIs - adquirir um token da web de chamadas à API web protegida](scenario-web-api-call-api-acquire-token.md), chamado nas ações dos controladores de API, chamando a API downstream (chamada de lista de tarefas).

Depois que você adquiriu o token, usá-lo como um token de portador para chamar a API downstream.

```CSharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para a produção](scenario-web-api-call-api-production.md)
