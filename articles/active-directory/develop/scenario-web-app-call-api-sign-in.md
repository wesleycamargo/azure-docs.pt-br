---
title: Aplicativo Web que chamadas de web APIs (entrar) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chamadas APIs (entrar) da web
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074554"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Aplicativo Web que chama as APIs da web - entrar

Você já sabe como adicionar entrada a seu aplicativo web. Você aprenderá que no [aplicativo Web que usuários entrar - Adicionar entrada](scenario-web-app-sign-user-sign-in.md).

O que é diferente aqui, é que, quando o usuário se conectou-out desse aplicativo ou de qualquer aplicativo, você deseja remover do cache de token, os tokens associados ao usuário.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Interceptando o retorno de chamada depois de sair - saída única

Seu aplicativo pode interceptar o depois `logout` evento, por exemplo para limpar a entrada de cache de token associado com a conta que desconectado. Vamos ver na segunda parte deste tutorial (sobre o aplicativo Web chamando uma API Web), que o aplicativo web armazena tokens de acesso para o usuário em um cache. Interceptando o depois `logout` retorno de chamada permite que seu aplicativo web para remover o usuário do cache de token. Esse mecanismo é ilustrado na `AddMsal()` método de [StartupHelper.cs L137 143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

O **Url de logoff** que você registrou para seu aplicativo permite que você implemente o logout único. A plataforma de identidade da Microsoft `logout` chamará o ponto de extremidade de **URL de logoff** registrado com seu aplicativo. Essa chamada ocorre se a saída foi iniciada a partir de seu aplicativo web ou outro aplicativo web ou o navegador. Para obter mais informações, consulte [logout único](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) na documentação conceitual.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo web](scenario-web-app-call-api-acquire-token.md)
