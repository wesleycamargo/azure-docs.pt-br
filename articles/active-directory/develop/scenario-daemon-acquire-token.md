---
title: Chamada de aplicativo de daemon às APIs da web (aquisição de tokens para o aplicativo) - plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chamadas de web APIs (aquisição de tokens)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075364"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplicativo daemon que chama APIs - web adquirir um token

Depois que o aplicativo cliente confidencial é construído, você pode adquirir um token para o aplicativo chamando ``AcquireTokenForClient``, passando o escopo e forçar ou não uma atualização do token.

## <a name="scopes-to-request"></a>Escopos para solicitar

O escopo para a solicitação de um fluxo de credenciais do cliente é o nome do recurso seguido por `/.default`. Essa notação informa ao Azure AD para usar o **permissões de nível de aplicativo** declarado estaticamente durante o registro de aplicativo. Além disso, como visto anteriormente, essas permissões de API devem ser concedidas por um administrador de locatários

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

Na MSAL. Python, o arquivo de configuração pareceria com o seguinte trecho de código:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Todos

O escopo usado para as credenciais do cliente deve ser sempre resourceId + "/ padrão"

### <a name="case-of-v10-resources"></a>Caso de recursos de v 1.0

> [!IMPORTANT]
> Para a MSAL (ponto de extremidade v2.0) solicitando um token de acesso para um recurso de aceitar um token de acesso de v 1.0, AD do Azure analisa o público-alvo desejado do escopo solicitado considerando tudo antes da última barra e usá-lo como o identificador de recurso.
> Portanto se, como o SQL Azure (**https://database.windows.net**) o recurso espera um público-alvo terminando com uma barra (para SQL Azure: `https://database.windows.net/`), você precisará solicitar um escopo de `https://database.windows.net//.default` (Observe as barras duplas). Consulte também MSAL.NET problema [747 #](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Uma barra à direita do recurso da url for omitida, o que causou a falha de autenticação do sql.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

### <a name="net"></a>.NET

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

#### <a name="application-token-cache"></a>Cache de token de aplicativo

No MSAL.NET, `AcquireTokenForClient` usa o **cache de token de aplicativo** (todos os outros métodos de AcquireTokenXX usam o cache de token de usuário) não chame `AcquireTokenSilent` antes de chamar `AcquireTokenForClient` como `AcquireTokenSilent` usa o **usuário** cache de token. `AcquireTokenForClient` verifica a **aplicativo** token do cache em si e o atualiza.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

Se você não tiver uma biblioteca para a linguagem de sua escolha, você talvez queira usar o protocolo diretamente:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Primeiro caso: solicitação de token de acesso com um segredo compartilhado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitação de token de acesso com um certificado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

### <a name="learn-more-about-the-protocol"></a>Saiba mais sobre o protocolo

Para obter mais informações, consulte a documentação do protocolo: [Fluxo de credenciais do Azure Active Directory v2.0 e cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>solução de problemas

### <a name="did-you-use-the-resourcedefault-scope"></a>Você usou o escopo do recurso/padrão?

Se você receber uma mensagem de erro informando que você usou um escopo inválido, você provavelmente não usou o `resource/.default` escopo.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Você esqueceu de fornecer o consentimento do administrador? Aplicativos daemon necessário!

Se você receber um erro ao chamar a API **privilégios insuficientes para concluir a operação**, o administrador de inquilinos precisa conceder permissões para o aplicativo. Consulte a etapa 6 de registrar o aplicativo de cliente acima.
Você normalmente verá e erro, como a descrição de erro a seguir:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo daemon - chamar uma API web](scenario-daemon-call-api.md)