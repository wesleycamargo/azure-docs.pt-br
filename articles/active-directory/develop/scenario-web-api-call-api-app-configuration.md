---
title: Web API que chamadas downstream APIs da web (configuração do código do aplicativo) – plataforma de identidade da Microsoft
description: Saiba como criar uma API que chamadas de web (configuração de código do aplicativo) de APIs da web
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
ms.openlocfilehash: 204baac37254592c0dc808af413fd8b3c6c79864
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074779"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Web API que chama APIs - configuração de código de web

Depois que você registrou sua API da web, você pode configurar o código do aplicativo.

O código para configurar sua API da web para que ele chame as APIs da web downstream se baseia no código usado para projetar uma API da web. Para obter mais informações, consulte [API web - configuração de aplicativo protegida](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Código assinado OnTokenValidated

Sobre a configuração de código para quaisquer APIs da web protegida, você precisa inscrever-se a validação do token de portador que é recebida quando sua API é chamada:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Fluxo em nome de

O método AddAccountToCacheFromJwt() precisa:

- Criar uma instância de um aplicativo de cliente confidencial MSAL.
- Chamar `AcquireTokenOnBehalf` para trocar o token de portador que foi adquirido pelo cliente para a API da web, em relação a um token de portador para o mesmo usuário, mas para nossa API para chamar uma API downstream.

### <a name="instantiate-a-confidential-client-application"></a>Criar uma instância de um aplicativo cliente confidencial

Esse fluxo só está disponível no fluxo de cliente confidencial, portanto, a API web protegida fornece as credenciais do cliente (segredo do cliente ou certificado) para o [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.confidentialclientapplicationbuilder?view=azure-dotnet-preview) por meio de `WithClientSecret` ou `WithCertificate`métodos, respectivamente.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

### <a name="how-to-call-on-behalf-of"></a>Como chamar on-behalf-of

A chamada on-behalf-of (OBO) é feita chamando o [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenonbehalfofparameterbuilder?view=azure-dotnet-preview) método o `IConfidentialClientApplication` interface.

O `ClientAssertion` baseia-se do token de portador recebido pela API da web de seus próprios clientes. Há [dois construtores](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), que recebe um portador JWT token e outra que usa qualquer tipo de declaração de usuário (outro tipo de token de segurança, o tipo é especificado, em seguida, em um parâmetro adicional chamado `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Na prática, o fluxo OBO geralmente é usado para adquirir um token para a API downstream e armazená-lo no cache de token de usuário MSAL.NET para que outras partes da API web podem chamar mais tarde na [substituições](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de ``AcquireTokenOnSilent`` para chamar as APIs de downstream. Isso tem o efeito de atualização de tokens, se necessário.

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
 try
 {
  UserAssertion userAssertion;
  IEnumerable<string> requestedScopes;
  if (jwtToken != null)
  {
   userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
   requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
  }
  else
  {
   throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
  }

  // Create the application
  var application = BuildConfidentialClientApplication(httpContext, principal);

  // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
  var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                  userAssertion)
                                        .ExecuteAsync()
                                        .GetAwaiter().GetResult();
 }
 catch (MsalException ex)
 {
  Debug.WriteLine(ex.Message);
  throw;
 }
}
```

## <a name="protocol"></a>Protocol

Para obter mais informações sobre o protocolo on-behalf-of, consulte [plataforma de identidade da Microsoft e de fluxo em nome do OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo](scenario-web-api-call-api-acquire-token.md)
