---
title: Que outras chamadas às APIs da web (adquirir um token para o aplicativo) - plataforma de identidade da Microsoft de API da Web
description: Saiba como criar uma API que chama outras web (adquirindo um token para o aplicativo) de APIs da web.
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231102"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>API da Web que chama APIs - web adquirir um token para o aplicativo

Depois de criar um aplicativo cliente de objeto, usá-lo para adquirir um token que você pode usar para chamar uma API web.

## <a name="code-in-the-controller"></a>O controlador de código

Aqui está um exemplo de código que será chamado nas ações de controladores de API, chamando a API downstream (chamada de lista de tarefas).

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
}
```

`BuildConfidentialClient()` é semelhante ao que você viu neste artigo [APIs - configuração de aplicativo da web de API da Web que chama](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` cria uma instância `IConfidentialClientApplication` com um cache que contém apenas informações para uma conta. A conta é fornecida pelo `GetAccountIdentifier` método.

O `GetAccountIdentifier` método usa as declarações associadas com a identidade do usuário para o qual a API da web recebeu o JWT:

```CSharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamar uma API web](scenario-web-api-call-api-call-api.md)
